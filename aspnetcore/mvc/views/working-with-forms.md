---
title: ASP.NET Core 表单中的标记帮助程序
author: rick-anderson
description: 介绍与表单配合使用的内置标记帮助程序。
ms.author: riande
ms.custom: mvc
ms.date: 1/11/2019
uid: mvc/views/working-with-forms
ms.openlocfilehash: cd15c641fbf702071bd57510a1d51737f6ab8e19
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099008"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="c931b-103">ASP.NET Core 表单中的标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c931b-103">Tag Helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="c931b-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Dave Paquette](https://twitter.com/Dave_Paquette) 和 [Jerrie Pelser](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="c931b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="c931b-105">本文档演示如何使用表单和表单中常用的 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="c931b-105">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="c931b-106">HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) 元素提供 Web 应用用于向服务器回发数据的主要机制。</span><span class="sxs-lookup"><span data-stu-id="c931b-106">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="c931b-107">本文档的大部分内容介绍[标记帮助程序](tag-helpers/intro.md)及其如何帮助高效创建可靠的 HTML 表单。</span><span class="sxs-lookup"><span data-stu-id="c931b-107">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="c931b-108">建议在阅读本文档前先阅读[标记帮助程序简介](tag-helpers/intro.md)。</span><span class="sxs-lookup"><span data-stu-id="c931b-108">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="c931b-109">在很多情况下，HTML 帮助程序为特定标记帮助程序提供了一种替代方法，但标记帮助程序不会替代 HTML 帮助程序，且并非每个 HTML 帮助程序都有对应的标记帮助程序，认识到这点也很重要。</span><span class="sxs-lookup"><span data-stu-id="c931b-109">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="c931b-110">如果存在 HTML 帮助程序替代项，文中会提到。</span><span class="sxs-lookup"><span data-stu-id="c931b-110">When an HTML Helper alternative exists, it's mentioned.</span></span>

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="c931b-111">表单标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c931b-111">The Form Tag Helper</span></span>

<span data-ttu-id="c931b-112">[表单](https://www.w3.org/TR/html401/interact/forms.html)标记帮助程序：</span><span class="sxs-lookup"><span data-stu-id="c931b-112">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="c931b-113">为 MVC 控制器操作或命名路由生成 HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` 属性值</span><span class="sxs-lookup"><span data-stu-id="c931b-113">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="c931b-114">生成隐藏的[请求验证令牌](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)，防止跨站点请求伪造（在 HTTP Post 操作方法中与 `[ValidateAntiForgeryToken]` 属性配合使用时）</span><span class="sxs-lookup"><span data-stu-id="c931b-114">Generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="c931b-115">提供 `asp-route-<Parameter Name>` 属性，其中 `<Parameter Name>` 添加到路由值。</span><span class="sxs-lookup"><span data-stu-id="c931b-115">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="c931b-116">`Html.BeginForm` 和 `Html.BeginRouteForm` 的 `routeValues` 参数提供类似的功能。</span><span class="sxs-lookup"><span data-stu-id="c931b-116">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="c931b-117">具有 HTML 帮助程序替代项 `Html.BeginForm` 和 `Html.BeginRouteForm`</span><span class="sxs-lookup"><span data-stu-id="c931b-117">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="c931b-118">示例:</span><span class="sxs-lookup"><span data-stu-id="c931b-118">Sample:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="c931b-119">上述表单标记帮助程序生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c931b-119">The Form Tag Helper above generates the following HTML:</span></span>

```HTML
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

<span data-ttu-id="c931b-120">MVC 运行时通过表单标记帮助程序属性 `asp-controller` 和 `asp-action` 生成 `action` 属性值。</span><span class="sxs-lookup"><span data-stu-id="c931b-120">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="c931b-121">表单标记帮助程序还会生成隐藏的[请求验证令牌](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)，防止跨站点请求伪造（在 HTTP Post 操作方法中与 `[ValidateAntiForgeryToken]` 属性配合使用时）。</span><span class="sxs-lookup"><span data-stu-id="c931b-121">The Form Tag Helper also generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="c931b-122">保护纯 HTML 表单免受跨站点请求伪造的影响很难，但表单标记帮助程序可提供此服务。</span><span class="sxs-lookup"><span data-stu-id="c931b-122">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="c931b-123">使用命名路由</span><span class="sxs-lookup"><span data-stu-id="c931b-123">Using a named route</span></span>

<span data-ttu-id="c931b-124">`asp-route` 标记帮助程序属性还可为 HTML `action` 属性生成标记。</span><span class="sxs-lookup"><span data-stu-id="c931b-124">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="c931b-125">具有名为 `register` 的[路由](../../fundamentals/routing.md)的应用可将以下标记用于注册页：</span><span class="sxs-lookup"><span data-stu-id="c931b-125">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="c931b-126">*Views/Account* 文件夹中的许多视图（在新建使用*个人用户帐户*身份验证的 Web 应用时生成）包含 [asp-route-returnurl](xref:mvc/views/working-with-forms) 属性：</span><span class="sxs-lookup"><span data-stu-id="c931b-126">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts*) contain the [asp-route-returnurl](xref:mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="c931b-127">使用内置模板时，`returnUrl` 仅会在用户尝试访问授权资源，但未验证身份或未获得授权的情况下自动填充。</span><span class="sxs-lookup"><span data-stu-id="c931b-127">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="c931b-128">如果尝试执行未经授权的访问，安全中间件会使用 `returnUrl` 集将用户重定向至登录页。</span><span class="sxs-lookup"><span data-stu-id="c931b-128">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-input-tag-helper"></a><span data-ttu-id="c931b-129">输入标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c931b-129">The Input Tag Helper</span></span>

<span data-ttu-id="c931b-130">输入标记帮助程序将 HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) 元素绑定到 Razor 视图中的模型表达式。</span><span class="sxs-lookup"><span data-stu-id="c931b-130">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="c931b-131">语法：</span><span class="sxs-lookup"><span data-stu-id="c931b-131">Syntax:</span></span>

```HTML
<input asp-for="<Expression Name>" />
```

<span data-ttu-id="c931b-132">输入标记帮助程序：</span><span class="sxs-lookup"><span data-stu-id="c931b-132">The Input Tag Helper:</span></span>

* <span data-ttu-id="c931b-133">为 `asp-for` 属性中指定的表达式名称生成 `id` 和 `name` HTML 属性。</span><span class="sxs-lookup"><span data-stu-id="c931b-133">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="c931b-134">`asp-for="Property1.Property2"` 与 `m => m.Property1.Property2` 相等。</span><span class="sxs-lookup"><span data-stu-id="c931b-134">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="c931b-135">表达式的名称用于 `asp-for` 属性值。</span><span class="sxs-lookup"><span data-stu-id="c931b-135">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="c931b-136">有关其他信息，请参阅[表达式名称](#expression-names)部分。</span><span class="sxs-lookup"><span data-stu-id="c931b-136">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="c931b-137">根据模型类型和应用于模型属性的[数据注释](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)特性设置 HTML `type` 特性值</span><span class="sxs-lookup"><span data-stu-id="c931b-137">Sets the HTML `type` attribute value based on the model type and  [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="c931b-138">如果已经指定，不会覆盖 HTML `type` 属性值</span><span class="sxs-lookup"><span data-stu-id="c931b-138">Won't overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="c931b-139">通过应用于模型属性的[数据注释](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)特性生成 [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) 验证特性</span><span class="sxs-lookup"><span data-stu-id="c931b-139">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="c931b-140">具有与 `Html.TextBoxFor` 和 `Html.EditorFor` 重叠的 HTML 帮助程序功能。</span><span class="sxs-lookup"><span data-stu-id="c931b-140">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="c931b-141">有关详细信息，请参阅**输入标记帮助程序的 HTML 帮助程序替代项**部分。</span><span class="sxs-lookup"><span data-stu-id="c931b-141">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="c931b-142">提供强类型化。</span><span class="sxs-lookup"><span data-stu-id="c931b-142">Provides strong typing.</span></span> <span data-ttu-id="c931b-143">如果属性的名称更改，但未更新标记帮助程序，则会收到类似如下内容的错误：</span><span class="sxs-lookup"><span data-stu-id="c931b-143">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

```HTML
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

<span data-ttu-id="c931b-144">`Input` 标记帮助程序根据 .NET 类型设置 HTML `type` 属性。</span><span class="sxs-lookup"><span data-stu-id="c931b-144">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="c931b-145">下表列出一些常见的 .NET 类型和生成的 HTML 类型（并未列出每个 .NET 类型）。</span><span class="sxs-lookup"><span data-stu-id="c931b-145">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="c931b-146">.NET 类型</span><span class="sxs-lookup"><span data-stu-id="c931b-146">.NET type</span></span>|<span data-ttu-id="c931b-147">输入类型</span><span class="sxs-lookup"><span data-stu-id="c931b-147">Input Type</span></span>|
|---|---|
|<span data-ttu-id="c931b-148">Bool</span><span class="sxs-lookup"><span data-stu-id="c931b-148">Bool</span></span>|<span data-ttu-id="c931b-149">type="checkbox"</span><span class="sxs-lookup"><span data-stu-id="c931b-149">type="checkbox"</span></span>|
|<span data-ttu-id="c931b-150">String</span><span class="sxs-lookup"><span data-stu-id="c931b-150">String</span></span>|<span data-ttu-id="c931b-151">type="text"</span><span class="sxs-lookup"><span data-stu-id="c931b-151">type="text"</span></span>|
|<span data-ttu-id="c931b-152">DateTime</span><span class="sxs-lookup"><span data-stu-id="c931b-152">DateTime</span></span>|<span data-ttu-id="c931b-153">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span><span class="sxs-lookup"><span data-stu-id="c931b-153">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span></span>|
|<span data-ttu-id="c931b-154">Byte</span><span class="sxs-lookup"><span data-stu-id="c931b-154">Byte</span></span>|<span data-ttu-id="c931b-155">type="number"</span><span class="sxs-lookup"><span data-stu-id="c931b-155">type="number"</span></span>|
|<span data-ttu-id="c931b-156">Int</span><span class="sxs-lookup"><span data-stu-id="c931b-156">Int</span></span>|<span data-ttu-id="c931b-157">type="number"</span><span class="sxs-lookup"><span data-stu-id="c931b-157">type="number"</span></span>|
|<span data-ttu-id="c931b-158">Single、Double</span><span class="sxs-lookup"><span data-stu-id="c931b-158">Single, Double</span></span>|<span data-ttu-id="c931b-159">type="number"</span><span class="sxs-lookup"><span data-stu-id="c931b-159">type="number"</span></span>|


<span data-ttu-id="c931b-160">下表显示输入标记帮助程序会映射到特定输入类型的一些常见[数据注释](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)属性（并未列出每个验证属性）：</span><span class="sxs-lookup"><span data-stu-id="c931b-160">The following table shows some common [data annotations](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>


|<span data-ttu-id="c931b-161">特性</span><span class="sxs-lookup"><span data-stu-id="c931b-161">Attribute</span></span>|<span data-ttu-id="c931b-162">输入类型</span><span class="sxs-lookup"><span data-stu-id="c931b-162">Input Type</span></span>|
|---|---|
|<span data-ttu-id="c931b-163">[EmailAddress]</span><span class="sxs-lookup"><span data-stu-id="c931b-163">[EmailAddress]</span></span>|<span data-ttu-id="c931b-164">type="email"</span><span class="sxs-lookup"><span data-stu-id="c931b-164">type="email"</span></span>|
|<span data-ttu-id="c931b-165">[Url]</span><span class="sxs-lookup"><span data-stu-id="c931b-165">[Url]</span></span>|<span data-ttu-id="c931b-166">type="url"</span><span class="sxs-lookup"><span data-stu-id="c931b-166">type="url"</span></span>|
|<span data-ttu-id="c931b-167">[HiddenInput]</span><span class="sxs-lookup"><span data-stu-id="c931b-167">[HiddenInput]</span></span>|<span data-ttu-id="c931b-168">type="hidden"</span><span class="sxs-lookup"><span data-stu-id="c931b-168">type="hidden"</span></span>|
|<span data-ttu-id="c931b-169">[Phone]</span><span class="sxs-lookup"><span data-stu-id="c931b-169">[Phone]</span></span>|<span data-ttu-id="c931b-170">type="tel"</span><span class="sxs-lookup"><span data-stu-id="c931b-170">type="tel"</span></span>|
|<span data-ttu-id="c931b-171">[DataType(DataType.Password)]</span><span class="sxs-lookup"><span data-stu-id="c931b-171">[DataType(DataType.Password)]</span></span>| <span data-ttu-id="c931b-172">type="password"</span><span class="sxs-lookup"><span data-stu-id="c931b-172">type="password"</span></span>|
|<span data-ttu-id="c931b-173">[DataType(DataType.Date)]</span><span class="sxs-lookup"><span data-stu-id="c931b-173">[DataType(DataType.Date)]</span></span>| <span data-ttu-id="c931b-174">type="date"</span><span class="sxs-lookup"><span data-stu-id="c931b-174">type="date"</span></span>|
|<span data-ttu-id="c931b-175">[DataType(DataType.Time)]</span><span class="sxs-lookup"><span data-stu-id="c931b-175">[DataType(DataType.Time)]</span></span>| <span data-ttu-id="c931b-176">type="time"</span><span class="sxs-lookup"><span data-stu-id="c931b-176">type="time"</span></span>|


<span data-ttu-id="c931b-177">示例:</span><span class="sxs-lookup"><span data-stu-id="c931b-177">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="c931b-178">上述代码生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c931b-178">The code above generates the following HTML:</span></span>

```HTML
  <form method="post" action="/Demo/RegisterInput">
       Email:
       <input type="email" data-val="true"
              data-val-email="The Email Address field is not a valid email address."
              data-val-required="The Email Address field is required."
              id="Email" name="Email" value="" /> <br>
       Password:
       <input type="password" data-val="true"
              data-val-required="The Password field is required."
              id="Password" name="Password" /><br>
       <button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
   </form>
```

<span data-ttu-id="c931b-179">应用于 `Email` 和 `Password` 属性的数据注释在模型中生成元数据。</span><span class="sxs-lookup"><span data-stu-id="c931b-179">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="c931b-180">输入标记帮助程序使用模型元数据并生成 [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` 属性（请参阅[模型验证](../models/validation.md)）。</span><span class="sxs-lookup"><span data-stu-id="c931b-180">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="c931b-181">这些属性描述要附加到输入字段的验证程序。</span><span class="sxs-lookup"><span data-stu-id="c931b-181">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="c931b-182">这样可以提供非介入式 HTML5 和 [jQuery](https://jquery.com/) 验证。</span><span class="sxs-lookup"><span data-stu-id="c931b-182">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="c931b-183">非介入式属性具有格式 `data-val-rule="Error Message"`，其中规则是验证规则的名称（例如 `data-val-required`、`data-val-email`、`data-val-maxlength` 等）。如果在属性中提供错误消息，则该错误消息会作为 `data-val-rule` 属性的值显示。</span><span class="sxs-lookup"><span data-stu-id="c931b-183">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it's displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="c931b-184">还有表单 `data-val-ruleName-argumentName="argumentValue"` 的属性，这些属性提供有关规则的其他详细信息，例如，`data-val-maxlength-max="1024"`。</span><span class="sxs-lookup"><span data-stu-id="c931b-184">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="c931b-185">输入标记帮助程序的 HTML 帮助程序替代项</span><span class="sxs-lookup"><span data-stu-id="c931b-185">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="c931b-186">`Html.TextBox`、`Html.TextBoxFor`、`Html.Editor` 和 `Html.EditorFor` 与输入标记帮助程序的功能存在重叠。</span><span class="sxs-lookup"><span data-stu-id="c931b-186">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="c931b-187">输入标记帮助程序会自动设置 `type` 属性；而 `Html.TextBox` 和 `Html.TextBoxFor` 不会。</span><span class="sxs-lookup"><span data-stu-id="c931b-187">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` won't.</span></span> <span data-ttu-id="c931b-188">`Html.Editor` 和 `Html.EditorFor` 处理集合、复杂对象和模板；而输入标记帮助程序不会。</span><span class="sxs-lookup"><span data-stu-id="c931b-188">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper doesn't.</span></span> <span data-ttu-id="c931b-189">输入标记帮助程序、`Html.EditorFor` 和 `Html.TextBoxFor` 是强类型（使用 lambda 表达式）；而 `Html.TextBox` 和 `Html.Editor` 不是（使用表达式名称）。</span><span class="sxs-lookup"><span data-stu-id="c931b-189">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="c931b-190">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="c931b-190">HtmlAttributes</span></span>

<span data-ttu-id="c931b-191">`@Html.Editor()` 和 `@Html.EditorFor()` 在执行其默认模板时使用名为 `htmlAttributes` 的特殊 `ViewDataDictionary` 条目。</span><span class="sxs-lookup"><span data-stu-id="c931b-191">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="c931b-192">此行为可选择使用 `additionalViewData` 参数增强。</span><span class="sxs-lookup"><span data-stu-id="c931b-192">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="c931b-193">键“htmlAttributes”区分大小写。</span><span class="sxs-lookup"><span data-stu-id="c931b-193">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="c931b-194">键“htmlAttributes”的处理方式与传递到输入帮助程序的 `htmlAttributes` 对象（例如 `@Html.TextBox()`）的处理方式类似。</span><span class="sxs-lookup"><span data-stu-id="c931b-194">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```HTML
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="c931b-195">表达式名称</span><span class="sxs-lookup"><span data-stu-id="c931b-195">Expression names</span></span>

<span data-ttu-id="c931b-196">`asp-for` 属性值是 `ModelExpression`，并且是 lambda 表达式的右侧。</span><span class="sxs-lookup"><span data-stu-id="c931b-196">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="c931b-197">因此，`asp-for="Property1"` 在生成的代码中变成 `m => m.Property1`，这也是无需使用 `Model` 前缀的原因。</span><span class="sxs-lookup"><span data-stu-id="c931b-197">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="c931b-198">“\@”字符可用作内联表达式的开头，并移到 `m.` 前面：</span><span class="sxs-lookup"><span data-stu-id="c931b-198">You can use the "\@" character to start an inline expression and move before the `m.`:</span></span>

```HTML
@{
       var joe = "Joe";
   }
   <input asp-for="@joe" />
```

<span data-ttu-id="c931b-199">生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c931b-199">Generates the following:</span></span>

```HTML
<input type="text" id="joe" name="joe" value="Joe" />
```

<span data-ttu-id="c931b-200">使用集合属性时，`asp-for="CollectionProperty[23].Member"` 在 `i` 具有值 `23` 时生成与 `asp-for="CollectionProperty[i].Member"` 相同的名称。</span><span class="sxs-lookup"><span data-stu-id="c931b-200">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

<span data-ttu-id="c931b-201">在 ASP.NET Core MVC 计算 `ModelExpression` 的值时，它会检查多个源，包括 `ModelState`。</span><span class="sxs-lookup"><span data-stu-id="c931b-201">When ASP.NET Core MVC calculates the value of `ModelExpression`, it inspects several sources, including `ModelState`.</span></span> <span data-ttu-id="c931b-202">以 `<input type="text" asp-for="@Name" />` 为例。</span><span class="sxs-lookup"><span data-stu-id="c931b-202">Consider `<input type="text" asp-for="@Name" />`.</span></span> <span data-ttu-id="c931b-203">计算出的 `value` 属性是第一个非 null 值，属于：</span><span class="sxs-lookup"><span data-stu-id="c931b-203">The calculated `value` attribute is the first non-null value from:</span></span>

* <span data-ttu-id="c931b-204">带有“Name”键的 `ModelState` 条目。</span><span class="sxs-lookup"><span data-stu-id="c931b-204">`ModelState` entry with key "Name".</span></span>
* <span data-ttu-id="c931b-205">`Model.Name` 表达式的结果。</span><span class="sxs-lookup"><span data-stu-id="c931b-205">Result of the expression `Model.Name`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="c931b-206">导航子属性</span><span class="sxs-lookup"><span data-stu-id="c931b-206">Navigating child properties</span></span>

<span data-ttu-id="c931b-207">还可使用视图模型的属性路径导航到子属性。</span><span class="sxs-lookup"><span data-stu-id="c931b-207">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="c931b-208">设想一个包含子 `Address` 属性的更复杂的模型类。</span><span class="sxs-lookup"><span data-stu-id="c931b-208">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="c931b-209">在视图中，绑定到 `Address.AddressLine1`：</span><span class="sxs-lookup"><span data-stu-id="c931b-209">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="c931b-210">为 `Address.AddressLine1` 生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c931b-210">The following HTML is generated for `Address.AddressLine1`:</span></span>

```HTML
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="" />
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="c931b-211">表达式名称和集合</span><span class="sxs-lookup"><span data-stu-id="c931b-211">Expression names and Collections</span></span>

<span data-ttu-id="c931b-212">包含 `Colors` 数组的模型示例：</span><span class="sxs-lookup"><span data-stu-id="c931b-212">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="c931b-213">操作方法：</span><span class="sxs-lookup"><span data-stu-id="c931b-213">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
   {
       ViewData["Index"] = colorIndex;
       return View(GetPerson(id));
   }
```

<span data-ttu-id="c931b-214">以下 Razor 显示如何访问特定 `Color` 元素：</span><span class="sxs-lookup"><span data-stu-id="c931b-214">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="c931b-215">*Views/Shared/EditorTemplates/String.cshtml* 模板：</span><span class="sxs-lookup"><span data-stu-id="c931b-215">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="c931b-216">使用 `List<T>` 的示例：</span><span class="sxs-lookup"><span data-stu-id="c931b-216">Sample using `List<T>`:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="c931b-217">以下 Razor 演示如何循环访问集合：</span><span class="sxs-lookup"><span data-stu-id="c931b-217">The following Razor shows how to iterate over a collection:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="c931b-218">*Views/Shared/EditorTemplates/ToDoItem.cshtml* 模板：</span><span class="sxs-lookup"><span data-stu-id="c931b-218">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

<span data-ttu-id="c931b-219">应尽量在 `asp-for` 或 `Html.DisplayFor` 等效上下文中使用值 `foreach`。</span><span class="sxs-lookup"><span data-stu-id="c931b-219">`foreach` should be used if possible when the value is going to be used in an `asp-for` or `Html.DisplayFor` equivalent context.</span></span> <span data-ttu-id="c931b-220">一般情况下，`for` 优于 `foreach`（如果情况允许使用的话），因为它不需要分配枚举器，但在 LINQ 表达式中评估索引器的成本高昂，应最大限度地减少使用它。</span><span class="sxs-lookup"><span data-stu-id="c931b-220">In general, `for` is better than `foreach` (if the scenario allows it) because it doesn't need to allocate an enumerator; however, evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="c931b-221">上述带有注释的示例代码演示如何将 lambda 表达式替换为 `@` 运算符来访问列表中的每个 `ToDoItem`。</span><span class="sxs-lookup"><span data-stu-id="c931b-221">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="c931b-222">文本区标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c931b-222">The Textarea Tag Helper</span></span>

<span data-ttu-id="c931b-223">`Textarea Tag Helper` 标记帮助程序类似于输入标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="c931b-223">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="c931b-224">通过模型为 [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) 元素生成 `id` 和 `name` 属性以及数据验证属性。</span><span class="sxs-lookup"><span data-stu-id="c931b-224">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="c931b-225">提供强类型化。</span><span class="sxs-lookup"><span data-stu-id="c931b-225">Provides strong typing.</span></span>

* <span data-ttu-id="c931b-226">HTML 帮助程序替代项：`Html.TextAreaFor`</span><span class="sxs-lookup"><span data-stu-id="c931b-226">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="c931b-227">示例:</span><span class="sxs-lookup"><span data-stu-id="c931b-227">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="c931b-228">生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c931b-228">The following HTML is generated:</span></span>

```HTML
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

## <a name="the-label-tag-helper"></a><span data-ttu-id="c931b-229">标签标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c931b-229">The Label Tag Helper</span></span>

* <span data-ttu-id="c931b-230">在 [<label>](https://www.w3.org/wiki/HTML/Elements/label) 元素中为表达式名称生成标签描述和 `for` 属性</span><span class="sxs-lookup"><span data-stu-id="c931b-230">Generates the label caption and `for` attribute on a [<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="c931b-231">HTML 帮助程序替代项：`Html.LabelFor`。</span><span class="sxs-lookup"><span data-stu-id="c931b-231">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="c931b-232">`Label Tag Helper` 通过纯 HTML 标签元素提供如下优势：</span><span class="sxs-lookup"><span data-stu-id="c931b-232">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="c931b-233">可自动从 `Display` 属性中获取描述性标签值。</span><span class="sxs-lookup"><span data-stu-id="c931b-233">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="c931b-234">预期的显示名称可能会随时间变化，`Display` 属性和标签标记帮助程序的组合会在其被使用的所有位置应用 `Display`。</span><span class="sxs-lookup"><span data-stu-id="c931b-234">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="c931b-235">源代码中的标记更少</span><span class="sxs-lookup"><span data-stu-id="c931b-235">Less markup in source code</span></span>

* <span data-ttu-id="c931b-236">模型属性的强类型化。</span><span class="sxs-lookup"><span data-stu-id="c931b-236">Strong typing with the model property.</span></span>

<span data-ttu-id="c931b-237">示例:</span><span class="sxs-lookup"><span data-stu-id="c931b-237">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="c931b-238">为 `<label>` 元素生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c931b-238">The following HTML is generated for the `<label>` element:</span></span>

```HTML
<label for="Email">Email Address</label>
```

<span data-ttu-id="c931b-239">标签标记帮助程序生成“Email”的 `for` 属性值，即与 `<input>` 元素关联的 ID。</span><span class="sxs-lookup"><span data-stu-id="c931b-239">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="c931b-240">标记帮助程序生成一致的 `id` 和 `for` 元素，方便将其正确关联。</span><span class="sxs-lookup"><span data-stu-id="c931b-240">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="c931b-241">本示例中的描述来自 `Display` 属性。</span><span class="sxs-lookup"><span data-stu-id="c931b-241">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="c931b-242">如果模型不包含 `Display` 特性，描述将为表达式的属性名称。</span><span class="sxs-lookup"><span data-stu-id="c931b-242">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="c931b-243">验证标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c931b-243">The Validation Tag Helpers</span></span>

<span data-ttu-id="c931b-244">有两个验证标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="c931b-244">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="c931b-245">`Validation Message Tag Helper`（为模型中的单个属性显示验证消息）和 `Validation Summary Tag Helper`（显示验证错误的摘要）。</span><span class="sxs-lookup"><span data-stu-id="c931b-245">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="c931b-246">`Input Tag Helper` 根据模型类的数据注释属性将 HTML5 客户端验证属性添加到输入元素中。</span><span class="sxs-lookup"><span data-stu-id="c931b-246">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="c931b-247">同时在服务器上执行验证。</span><span class="sxs-lookup"><span data-stu-id="c931b-247">Validation is also performed on the server.</span></span> <span data-ttu-id="c931b-248">验证标记帮助程序会在发生验证错误时显示这些错误消息。</span><span class="sxs-lookup"><span data-stu-id="c931b-248">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="c931b-249">验证消息标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c931b-249">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="c931b-250">将 [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` 属性添加到 [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) 元素中，该元素会附加指定模型属性的输入字段中的验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="c931b-250">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="c931b-251">[jQuery](https://jquery.com/) 会在发生客户端验证错误时在 `<span>` 元素中显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="c931b-251">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="c931b-252">还会在服务器上执行验证。</span><span class="sxs-lookup"><span data-stu-id="c931b-252">Validation also takes place on the server.</span></span> <span data-ttu-id="c931b-253">客户端可能已禁用 JavaScript，一些验证仅可在服务器端执行。</span><span class="sxs-lookup"><span data-stu-id="c931b-253">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="c931b-254">HTML 帮助程序替代项：`Html.ValidationMessageFor`</span><span class="sxs-lookup"><span data-stu-id="c931b-254">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="c931b-255">`Validation Message Tag Helper` 与 HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) 元素中的 `asp-validation-for` 属性配合使用。</span><span class="sxs-lookup"><span data-stu-id="c931b-255">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```HTML
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="c931b-256">验证消息标记帮助程序会生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c931b-256">The Validation Message Tag Helper will generate the following HTML:</span></span>

```HTML
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="c931b-257">对于同一属性，通常在 `Input` 标记帮助程序后使用 `Validation Message Tag Helper`。</span><span class="sxs-lookup"><span data-stu-id="c931b-257">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="c931b-258">这样做可在导致错误的输入附近显示所有验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="c931b-258">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="c931b-259">必须拥有包含正确的 JavaScript 和 [jQuery](https://jquery.com/) 脚本引用的视图才能执行客户端验证。</span><span class="sxs-lookup"><span data-stu-id="c931b-259">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="c931b-260">有关详细信息，请参阅[模型验证](../models/validation.md)。</span><span class="sxs-lookup"><span data-stu-id="c931b-260">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="c931b-261">发生服务器端验证错误时（例如，禁用自定义服务器端验证或客户端验证时），MVC 会将该错误消息作为 `<span>` 元素的主体。</span><span class="sxs-lookup"><span data-stu-id="c931b-261">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```HTML
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="c931b-262">验证摘要标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c931b-262">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="c931b-263">针对具有 `asp-validation-summary` 属性的 `<div>` 元素</span><span class="sxs-lookup"><span data-stu-id="c931b-263">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="c931b-264">HTML 帮助程序替代项：`@Html.ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="c931b-264">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="c931b-265">`Validation Summary Tag Helper` 用于显示验证消息的摘要。</span><span class="sxs-lookup"><span data-stu-id="c931b-265">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="c931b-266">`asp-validation-summary` 属性值可以是以下任意值：</span><span class="sxs-lookup"><span data-stu-id="c931b-266">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="c931b-267">asp-validation-summary</span><span class="sxs-lookup"><span data-stu-id="c931b-267">asp-validation-summary</span></span>|<span data-ttu-id="c931b-268">显示的验证消息</span><span class="sxs-lookup"><span data-stu-id="c931b-268">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="c931b-269">ValidationSummary.All</span><span class="sxs-lookup"><span data-stu-id="c931b-269">ValidationSummary.All</span></span>|<span data-ttu-id="c931b-270">属性和模型级别</span><span class="sxs-lookup"><span data-stu-id="c931b-270">Property and model level</span></span>|
|<span data-ttu-id="c931b-271">ValidationSummary.ModelOnly</span><span class="sxs-lookup"><span data-stu-id="c931b-271">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="c931b-272">模型</span><span class="sxs-lookup"><span data-stu-id="c931b-272">Model</span></span>|
|<span data-ttu-id="c931b-273">ValidationSummary.None</span><span class="sxs-lookup"><span data-stu-id="c931b-273">ValidationSummary.None</span></span>|<span data-ttu-id="c931b-274">无</span><span class="sxs-lookup"><span data-stu-id="c931b-274">None</span></span>|

### <a name="sample"></a><span data-ttu-id="c931b-275">示例</span><span class="sxs-lookup"><span data-stu-id="c931b-275">Sample</span></span>

<span data-ttu-id="c931b-276">在以下示例中，数据模型使用 `DataAnnotation` 属性修饰，在 `<input>` 元素中生成验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="c931b-276">In the following example, the data model is decorated with `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="c931b-277">验证标记帮助程序会在发生验证错误时显示错误消息：</span><span class="sxs-lookup"><span data-stu-id="c931b-277">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="c931b-278">生成的 HTML（如果模型有效）：</span><span class="sxs-lookup"><span data-stu-id="c931b-278">The generated HTML (when the model is valid):</span></span>

```HTML
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"> <br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

## <a name="the-select-tag-helper"></a><span data-ttu-id="c931b-279">选择标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c931b-279">The Select Tag Helper</span></span>

* <span data-ttu-id="c931b-280">为模型属性生成 [select](https://www.w3.org/wiki/HTML/Elements/select) 元素和关联的 [option](https://www.w3.org/wiki/HTML/Elements/option) 元素。</span><span class="sxs-lookup"><span data-stu-id="c931b-280">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="c931b-281">具有 HTML 帮助程序替代项 `Html.DropDownListFor` 和 `Html.ListBoxFor`</span><span class="sxs-lookup"><span data-stu-id="c931b-281">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="c931b-282">`Select Tag Helper` `asp-for` 为 [select](https://www.w3.org/wiki/HTML/Elements/select) 元素指定模型属性名称，`asp-items` 指定 [option](https://www.w3.org/wiki/HTML/Elements/option) 元素。</span><span class="sxs-lookup"><span data-stu-id="c931b-282">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="c931b-283">例如:</span><span class="sxs-lookup"><span data-stu-id="c931b-283">For example:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="c931b-284">示例:</span><span class="sxs-lookup"><span data-stu-id="c931b-284">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="c931b-285">`Index` 方法初始化 `CountryViewModel`，设置选定的国家/地区并将其传递到 `Index` 视图。</span><span class="sxs-lookup"><span data-stu-id="c931b-285">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

<span data-ttu-id="c931b-286">HTTP POST `Index` 方法显示选定内容：</span><span class="sxs-lookup"><span data-stu-id="c931b-286">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="c931b-287">`Index` 视图：</span><span class="sxs-lookup"><span data-stu-id="c931b-287">The `Index` view:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="c931b-288">生成以下 HTML（选择“CA”时）：</span><span class="sxs-lookup"><span data-stu-id="c931b-288">Which generates the following HTML (with "CA" selected):</span></span>

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
   </form>
```

> [!NOTE]
> <span data-ttu-id="c931b-289">不建议将 `ViewBag` 或 `ViewData` 与选择标记帮助程序配合使用。</span><span class="sxs-lookup"><span data-stu-id="c931b-289">We don't recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="c931b-290">视图模型在提供 MVC 元数据方面更可靠且通常更不容易出现问题。</span><span class="sxs-lookup"><span data-stu-id="c931b-290">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="c931b-291">`asp-for` 属性值是特殊情况，它不要求提供 `Model` 前缀，但其他标记帮助程序属性需要该前缀（例如 `asp-items`）</span><span class="sxs-lookup"><span data-stu-id="c931b-291">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="c931b-292">枚举绑定</span><span class="sxs-lookup"><span data-stu-id="c931b-292">Enum binding</span></span>

<span data-ttu-id="c931b-293">通常可方便地将 `<select>` 与 `enum` 属性配合使用并通过 `enum` 值生成 `SelectListItem` 元素。</span><span class="sxs-lookup"><span data-stu-id="c931b-293">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="c931b-294">示例:</span><span class="sxs-lookup"><span data-stu-id="c931b-294">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="c931b-295">`GetEnumSelectList` 方法为枚举生成 `SelectList` 对象。</span><span class="sxs-lookup"><span data-stu-id="c931b-295">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="c931b-296">可使用 `Display` 属性修饰枚举器列表，以获取更丰富的 UI：</span><span class="sxs-lookup"><span data-stu-id="c931b-296">You can decorate your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="c931b-297">生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c931b-297">The following HTML is generated:</span></span>

```HTML
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
    </form>
```

### <a name="option-group"></a><span data-ttu-id="c931b-298">选项组</span><span class="sxs-lookup"><span data-stu-id="c931b-298">Option Group</span></span>

<span data-ttu-id="c931b-299">如果视图模型包含一个或多个 `SelectListGroup` 对象，则会生成 HTML [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) 元素。</span><span class="sxs-lookup"><span data-stu-id="c931b-299">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="c931b-300">`CountryViewModelGroup` 将 `SelectListItem` 元素分组为“North America”组和“Europe”组：</span><span class="sxs-lookup"><span data-stu-id="c931b-300">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="c931b-301">两个组如下所示：</span><span class="sxs-lookup"><span data-stu-id="c931b-301">The two groups are shown below:</span></span>

![选项组示例](working-with-forms/_static/grp.png)

<span data-ttu-id="c931b-303">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="c931b-303">The generated HTML:</span></span>

```HTML
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
 </form>
```

### <a name="multiple-select"></a><span data-ttu-id="c931b-304">多重选择</span><span class="sxs-lookup"><span data-stu-id="c931b-304">Multiple select</span></span>

<span data-ttu-id="c931b-305">如果 `asp-for` 属性中指定的属性为 `IEnumerable`，选择标记帮助程序会自动生成 [multiple = "multiple"](http://w3c.github.io/html-reference/select.html) 属性。</span><span class="sxs-lookup"><span data-stu-id="c931b-305">The Select Tag Helper  will automatically generate the [multiple = "multiple"](http://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="c931b-306">例如，如果给定以下模型：</span><span class="sxs-lookup"><span data-stu-id="c931b-306">For example, given the following model:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="c931b-307">及以下视图：</span><span class="sxs-lookup"><span data-stu-id="c931b-307">With the following view:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="c931b-308">则会生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c931b-308">Generates the following HTML:</span></span>

```HTML
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

### <a name="no-selection"></a><span data-ttu-id="c931b-309">无选定内容</span><span class="sxs-lookup"><span data-stu-id="c931b-309">No selection</span></span>

<span data-ttu-id="c931b-310">如果发现自己在多个页面中使用“未指定”选项，可创建模板用于消除重复的 HTML：</span><span class="sxs-lookup"><span data-stu-id="c931b-310">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="c931b-311">*Views/Shared/EditorTemplates/CountryViewModel.cshtml* 模板：</span><span class="sxs-lookup"><span data-stu-id="c931b-311">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="c931b-312">添加 HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) 元素并不局限于*无选定内容*用例。</span><span class="sxs-lookup"><span data-stu-id="c931b-312">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements isn't limited to the *No selection* case.</span></span> <span data-ttu-id="c931b-313">例如，以下视图和操作方法会生成与上述代码类似的 HTML：</span><span class="sxs-lookup"><span data-stu-id="c931b-313">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

[!code-HTML[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="c931b-314">根据当前的 `Country` 值选择正确的 `<option>` 元素（包含 `selected="selected"` 属性）。</span><span class="sxs-lookup"><span data-stu-id="c931b-314">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

```HTML
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
 </form>
 ```

## <a name="additional-resources"></a><span data-ttu-id="c931b-315">其他资源</span><span class="sxs-lookup"><span data-stu-id="c931b-315">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* [<span data-ttu-id="c931b-316">HTML Form 元素</span><span class="sxs-lookup"><span data-stu-id="c931b-316">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)
* [<span data-ttu-id="c931b-317">请求验证令牌</span><span class="sxs-lookup"><span data-stu-id="c931b-317">Request Verification Token</span></span>](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [<span data-ttu-id="c931b-318">IAttributeAdapter 接口</span><span class="sxs-lookup"><span data-stu-id="c931b-318">IAttributeAdapter Interface</span></span>](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [<span data-ttu-id="c931b-319">本文档的代码片段</span><span class="sxs-lookup"><span data-stu-id="c931b-319">Code snippets for this document</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
