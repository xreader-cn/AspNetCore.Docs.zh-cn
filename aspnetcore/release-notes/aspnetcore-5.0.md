---
title: ASP.NET Core 5.0 的新增功能
author: rick-anderson
description: 了解 ASP.NET Core 5.0 的新增功能。
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: 5caa412773bf9c8e3bed5ebc529d48b886de6956
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253067"
---
# <a name="whats-new-in-aspnet-core-50"></a><span data-ttu-id="ccd14-103">ASP.NET Core 5.0 的新增功能</span><span class="sxs-lookup"><span data-stu-id="ccd14-103">What's new in ASP.NET Core 5.0</span></span>

<span data-ttu-id="ccd14-104">本文重点介绍 ASP.NET Core 5.0 中最重要的更改，并提供相关文档的链接。</span><span class="sxs-lookup"><span data-stu-id="ccd14-104">This article highlights the most significant changes in ASP.NET Core 5.0 with links to relevant documentation.</span></span>

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a><span data-ttu-id="ccd14-105">ASP.NET Core MVC 和 Razor 改进</span><span class="sxs-lookup"><span data-stu-id="ccd14-105">ASP.NET Core MVC and Razor improvements</span></span>

### <a name="model-binding-datetime-as-utc"></a><span data-ttu-id="ccd14-106">通过模型绑定将日期/时间绑定到 UTC</span><span class="sxs-lookup"><span data-stu-id="ccd14-106">Model binding DateTime as UTC</span></span>

<span data-ttu-id="ccd14-107">模型绑定现在支持将 UTC 时间字符串绑定到 `DateTime`。</span><span class="sxs-lookup"><span data-stu-id="ccd14-107">Model binding now supports binding UTC time strings to `DateTime`.</span></span> <span data-ttu-id="ccd14-108">如果请求包含 UTC 时间字符串，则模型绑定会将其绑定到 UTC `DateTime`。</span><span class="sxs-lookup"><span data-stu-id="ccd14-108">If the request contains a UTC time string, model binding binds it to a UTC `DateTime`.</span></span> <span data-ttu-id="ccd14-109">例如，以下时间字符串会绑定到 UTC `DateTime`：`https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span><span class="sxs-lookup"><span data-stu-id="ccd14-109">For example, the following time string is bound the UTC `DateTime`: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span></span>

### <a name="model-binding-and-validation-with-c-9-record-types"></a><span data-ttu-id="ccd14-110">模型绑定和验证与 C# 9 记录类型一起使用</span><span class="sxs-lookup"><span data-stu-id="ccd14-110">Model binding and validation with C# 9 record types</span></span>

<span data-ttu-id="ccd14-111">[C# 9 记录类型](/dotnet/csharp/whats-new/csharp-9#record-types)可以与 MVC 控制器或 Razor 页面中的模型绑定一起使用。</span><span class="sxs-lookup"><span data-stu-id="ccd14-111">[C# 9 record types](/dotnet/csharp/whats-new/csharp-9#record-types) can be used with model binding in an MVC controller or a Razor Page.</span></span> <span data-ttu-id="ccd14-112">记录类型是为通过网络传输的数据建模的好方法。</span><span class="sxs-lookup"><span data-stu-id="ccd14-112">Record types are a good way to model data being transmitted over the network.</span></span>

<span data-ttu-id="ccd14-113">例如，以下 `PersonController` 将 `Person` 记录类型与模型绑定和窗体验证一起使用：</span><span class="sxs-lookup"><span data-stu-id="ccd14-113">For example, the following `PersonController` uses the `Person` record type with model binding and form validation:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

<span data-ttu-id="ccd14-114">`Person/Index.cshtml` 文件：</span><span class="sxs-lookup"><span data-stu-id="ccd14-114">The `Person/Index.cshtml` file:</span></span>

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a><span data-ttu-id="ccd14-115">对 DynamicRouteValueTransformer 的改进</span><span class="sxs-lookup"><span data-stu-id="ccd14-115">Improvements to DynamicRouteValueTransformer</span></span>

<span data-ttu-id="ccd14-116">ASP.NET Core 3.1 引入了 <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer>，作为使用自定义终结点动态选择 MVC 控制器操作或 Razor 页面的方法。</span><span class="sxs-lookup"><span data-stu-id="ccd14-116">ASP.NET Core 3.1 introduced <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> as a way to use custom endpoint to dynamically select an MVC controller action or a Razor page.</span></span> <span data-ttu-id="ccd14-117">ASP.NET Core 5.0 应用可以将状态传递到 `DynamicRouteValueTransformer` 并筛选选定的终结点集。</span><span class="sxs-lookup"><span data-stu-id="ccd14-117">ASP.NET Core 5.0 apps can pass state to a `DynamicRouteValueTransformer` and filter the set of endpoints chosen.</span></span>

### <a name="miscellaneous"></a><span data-ttu-id="ccd14-118">杂项</span><span class="sxs-lookup"><span data-stu-id="ccd14-118">Miscellaneous</span></span>

* <span data-ttu-id="ccd14-119">[[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) 特性可应用于 Razor 页面模型上的属性。</span><span class="sxs-lookup"><span data-stu-id="ccd14-119">The [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute can be applied to properties on a Razor Page model.</span></span>
* <span data-ttu-id="ccd14-120">默认情况下，从正文中绑定的参数和属性被视为必需。</span><span class="sxs-lookup"><span data-stu-id="ccd14-120">Parameters and properties bound from the body are considered required by default.</span></span> <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a><span data-ttu-id="ccd14-121">Web API</span><span class="sxs-lookup"><span data-stu-id="ccd14-121">Web API</span></span>

### <a name="openapi-specification-on-by-default"></a><span data-ttu-id="ccd14-122">OpenAPI 规范默认开启</span><span class="sxs-lookup"><span data-stu-id="ccd14-122">OpenAPI Specification on by default</span></span>

<span data-ttu-id="ccd14-123">[OpenAPI 规范](http://spec.openapis.org/oas/v3.0.3)是一种行业标准，用于描述 HTTP API 并将其集成到复杂的业务流程中或与第三方集成。</span><span class="sxs-lookup"><span data-stu-id="ccd14-123">[OpenAPI Specification](http://spec.openapis.org/oas/v3.0.3) is an industry standard for describing HTTP APIs and integrating them into complex business processes or with third parties.</span></span> <span data-ttu-id="ccd14-124">OpenAPI 受到所有云提供程序和许多 API 注册表的广泛支持。</span><span class="sxs-lookup"><span data-stu-id="ccd14-124">OpenAPI is widely supported by all cloud providers and many API registries.</span></span> <span data-ttu-id="ccd14-125">从 Web API 发出 OpenAPI 文档的应用具有各种可使用这些 API 的新机会。</span><span class="sxs-lookup"><span data-stu-id="ccd14-125">Apps that emit OpenAPI documents from web APIs have a variety of new opportunities in which those APIs can be used.</span></span> <span data-ttu-id="ccd14-126">与开放源代码项目 [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) 的维护人员合作，ASP.NET Core API 模板包含对 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 的 NuGet 依赖关系。</span><span class="sxs-lookup"><span data-stu-id="ccd14-126">In partnership with the maintainers of the open-source project [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), the ASP.NET Core API template contains a NuGet dependency on [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span></span> <span data-ttu-id="ccd14-127">Swashbuckle 是一个常用的开放源代码 NuGet 包，可动态发出 OpenAPI 文档。</span><span class="sxs-lookup"><span data-stu-id="ccd14-127">Swashbuckle is a popular open-source NuGet package that emits OpenAPI documents dynamically.</span></span> <span data-ttu-id="ccd14-128">Swashbuckle 通过 API 控制器进行自检并在运行时或在生成时使用 Swashbuckle CLI 生成 OpenAPI 文档来实现此目的。</span><span class="sxs-lookup"><span data-stu-id="ccd14-128">Swashbuckle does this by introspecting over the API controllers and generating the OpenAPI document at run-time, or at build time using the Swashbuckle CLI.</span></span>

<span data-ttu-id="ccd14-129">在 ASP.NET Core 5.0 中，Web API 模板默认启用 OpenAPI 支持。</span><span class="sxs-lookup"><span data-stu-id="ccd14-129">In ASP.NET Core 5.0, the web API templates enable the OpenAPI support by default.</span></span> <span data-ttu-id="ccd14-130">若要禁用 OpenAPI，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="ccd14-130">To disable OpenAPI:</span></span>

* <span data-ttu-id="ccd14-131">通过命令行：</span><span class="sxs-lookup"><span data-stu-id="ccd14-131">From the command line:</span></span>

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* <span data-ttu-id="ccd14-132">通过 Visual Studio：取消选中“启用 OpenAPI 支持”。</span><span class="sxs-lookup"><span data-stu-id="ccd14-132">From Visual Studio: Uncheck **Enable OpenAPI support**.</span></span>

<span data-ttu-id="ccd14-133">为 Web API 项目创建的所有 .csproj 文件都包含 [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet 包引用。</span><span class="sxs-lookup"><span data-stu-id="ccd14-133">All *.csproj* files created for web API projects contain the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet package reference.</span></span>

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

<span data-ttu-id="ccd14-134">模板生成的代码在 `Startup.ConfigureServices` 中包含用于激活 OpenAPI 文档生成的代码：</span><span class="sxs-lookup"><span data-stu-id="ccd14-134">The template generated code contains code in `Startup.ConfigureServices` that activates OpenAPI document generation:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

<span data-ttu-id="ccd14-135">`Startup.Configure` 方法将添加 Swashbuckle 中间件，这将启用：</span><span class="sxs-lookup"><span data-stu-id="ccd14-135">The `Startup.Configure` method adds the Swashbuckle middleware, which enables the:</span></span>

* <span data-ttu-id="ccd14-136">文档生成过程。</span><span class="sxs-lookup"><span data-stu-id="ccd14-136">Document generation process.</span></span>
* <span data-ttu-id="ccd14-137">在开发模式下默认为 Swagger UI 页。</span><span class="sxs-lookup"><span data-stu-id="ccd14-137">Swagger UI page by default in development mode.</span></span>

<span data-ttu-id="ccd14-138">在发布到生产环境时，模板生成的代码不会意外公开 API 的说明。</span><span class="sxs-lookup"><span data-stu-id="ccd14-138">The template generated code won't accidentally expose the API's description when publishing to production.</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a><span data-ttu-id="ccd14-139">Azure API 管理导入</span><span class="sxs-lookup"><span data-stu-id="ccd14-139">Azure API Management Import</span></span>

<span data-ttu-id="ccd14-140">ASP.NET Core API 项目启用 OpenAPI 时，Visual Studio 2019 版本 16.8 及更高版本将在发布流程中自动提供额外的步骤。</span><span class="sxs-lookup"><span data-stu-id="ccd14-140">When ASP.NET Core API projects enable OpenAPI, the Visual Studio 2019 version 16.8 and later publishing automatically offer an additional step in the publishing flow.</span></span> <span data-ttu-id="ccd14-141">使用 [Azure API 管理](xref:tutorials/publish-to-azure-api-management-using-vs)的开发人员有机会在发布流程中自动将 API 导入到 Azure API 管理：</span><span class="sxs-lookup"><span data-stu-id="ccd14-141">Developers who use [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) have an opportunity to automatically import the APIs into Azure API Management during the publish flow:</span></span>

![Azure API 管理导入与发布](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a><span data-ttu-id="ccd14-143">更佳的 Web API 项目启动体验</span><span class="sxs-lookup"><span data-stu-id="ccd14-143">Better launch experience for web API projects</span></span>

<span data-ttu-id="ccd14-144">如果默认启用了 OpenAPI，则显著改进了 Web API 开发人员的应用启动体验 (F5)。</span><span class="sxs-lookup"><span data-stu-id="ccd14-144">With OpenAPI enabled by default, the app launching experience (F5) for web API developers significantly improves.</span></span> <span data-ttu-id="ccd14-145">借助 ASP.NET Core 5.0，Web API 模板会预先配置为加载 Swagger UI 页。</span><span class="sxs-lookup"><span data-stu-id="ccd14-145">With ASP.NET Core 5.0, the web API template comes pre-configured to load up the Swagger UI page.</span></span> <span data-ttu-id="ccd14-146">Swagger UI 页提供为已发布的 API 添加的文档，并且单击一次即可测试 API。</span><span class="sxs-lookup"><span data-stu-id="ccd14-146">The Swagger UI page provides both the documentation added for the published API, and enables testing the APIs with a single click.</span></span>

![swagger/index.html 视图](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a><span data-ttu-id="ccd14-148">性能改进</span><span class="sxs-lookup"><span data-stu-id="ccd14-148">Performance improvements</span></span>

<span data-ttu-id="ccd14-149">对于 .NET 5，通过特别着重于复杂的 UI 呈现和 JSON 序列化显著改进了 Blazor WebAssembly 运行时性能。</span><span class="sxs-lookup"><span data-stu-id="ccd14-149">For .NET 5, we made significant improvements to Blazor WebAssembly runtime performance with a specific focus on complex UI rendering and JSON serialization.</span></span> <span data-ttu-id="ccd14-150">在我们的性能测试中，.NET 5 中 Blazor WebAssembly 的速度要比大多数情况快两到三倍。</span><span class="sxs-lookup"><span data-stu-id="ccd14-150">In our performance tests, Blazor WebAssembly in .NET 5 is two to three times faster for most scenarios.</span></span> <span data-ttu-id="ccd14-151">有关详细信息，请参阅 [ASP.NET 博客：.NET 5 候选发布版本 1 中的 ASP.NET Core 更新](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements)。</span><span class="sxs-lookup"><span data-stu-id="ccd14-151">For more information, see [ASP.NET Blog: ASP.NET Core updates in .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span></span>

### <a name="css-isolation"></a><span data-ttu-id="ccd14-152">CSS 隔离</span><span class="sxs-lookup"><span data-stu-id="ccd14-152">CSS isolation</span></span>

<span data-ttu-id="ccd14-153">Blazor 现在支持定义限定为给定组件的 CSS 样式。</span><span class="sxs-lookup"><span data-stu-id="ccd14-153">Blazor now supports defining CSS styles that are scoped to a given component.</span></span> <span data-ttu-id="ccd14-154">使用组件特定的 CSS 样式，可以更轻松地了解应用中的样式，并避免全局样式的意外副作用。</span><span class="sxs-lookup"><span data-stu-id="ccd14-154">Component-specific CSS styles make it easier to reason about the styles in an app and to avoid unintentional side effects of global styles.</span></span> <span data-ttu-id="ccd14-155">有关详细信息，请参阅 <xref:blazor/components/css-isolation>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-155">For more information, see <xref:blazor/components/css-isolation>.</span></span>

### <a name="new-inputfile-component"></a><span data-ttu-id="ccd14-156">新的 `InputFile` 组件</span><span class="sxs-lookup"><span data-stu-id="ccd14-156">New `InputFile` component</span></span>

<span data-ttu-id="ccd14-157">`InputFile` 组件允许读取用户选择要上传的一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="ccd14-157">The `InputFile` component allows reading one or more files selected by a user for upload.</span></span> <span data-ttu-id="ccd14-158">有关详细信息，请参阅 <xref:blazor/file-uploads>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-158">For more information, see <xref:blazor/file-uploads>.</span></span>

### <a name="new-inputradio-and-inputradiogroup-components"></a><span data-ttu-id="ccd14-159">新的 `InputRadio` 和 `InputRadioGroup` 组件</span><span class="sxs-lookup"><span data-stu-id="ccd14-159">New `InputRadio` and `InputRadioGroup` components</span></span>

<span data-ttu-id="ccd14-160">Blazor 具有内置的 `InputRadio` 和 `InputRadioGroup` 组件，这些组件可简化通过集成验证将数据绑定到单选按钮组。</span><span class="sxs-lookup"><span data-stu-id="ccd14-160">Blazor has built-in `InputRadio` and `InputRadioGroup` components that simplify data binding to radio button groups with integrated validation.</span></span> <span data-ttu-id="ccd14-161">有关详细信息，请参阅 <xref:blazor/forms-validation>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-161">For more information, see <xref:blazor/forms-validation>.</span></span>

### <a name="component-virtualization"></a><span data-ttu-id="ccd14-162">组件虚拟化</span><span class="sxs-lookup"><span data-stu-id="ccd14-162">Component virtualization</span></span>

<span data-ttu-id="ccd14-163">使用 Blazor 框架的内置虚拟化支持提高组件呈现的感知性能。</span><span class="sxs-lookup"><span data-stu-id="ccd14-163">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="ccd14-164">有关详细信息，请参阅 <xref:blazor/components/virtualization>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-164">For more information, see <xref:blazor/components/virtualization>.</span></span>

### <a name="ontoggle-event-support"></a><span data-ttu-id="ccd14-165">`ontoggle` 事件支持</span><span class="sxs-lookup"><span data-stu-id="ccd14-165">`ontoggle` event support</span></span>

<span data-ttu-id="ccd14-166">Blazor 事件现在支持 `ontoggle` DOM 事件。</span><span class="sxs-lookup"><span data-stu-id="ccd14-166">Blazor events now support the `ontoggle` DOM event.</span></span> <span data-ttu-id="ccd14-167">有关详细信息，请参阅 <xref:blazor/components/event-handling#event-argument-types>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-167">For more information, see <xref:blazor/components/event-handling#event-argument-types>.</span></span>

### <a name="set-ui-focus-in-no-locblazor-apps"></a><span data-ttu-id="ccd14-168">将 UI 焦点设置在 Blazor 应用中</span><span class="sxs-lookup"><span data-stu-id="ccd14-168">Set UI focus in Blazor apps</span></span>

<span data-ttu-id="ccd14-169">对元素引用使用 `FocusAsync` 便捷方法，以便将 UI 焦点设置到该元素。</span><span class="sxs-lookup"><span data-stu-id="ccd14-169">Use the `FocusAsync` convenience method on element references to set the UI focus to that element.</span></span> <span data-ttu-id="ccd14-170">有关详细信息，请参阅 <xref:blazor/components/event-handling#focus-an-element>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-170">For more information, see <xref:blazor/components/event-handling#focus-an-element>.</span></span>

### <a name="custom-validation-class-attributes"></a><span data-ttu-id="ccd14-171">自定义验证类属性</span><span class="sxs-lookup"><span data-stu-id="ccd14-171">Custom validation class attributes</span></span>

<span data-ttu-id="ccd14-172">与 CSS 框架集成时，自定义验证类名称非常有用，例如启动。</span><span class="sxs-lookup"><span data-stu-id="ccd14-172">Custom validation class names are useful when integrating with CSS frameworks, such as Bootstrap.</span></span> <span data-ttu-id="ccd14-173">有关详细信息，请参阅 <xref:blazor/forms-validation#custom-validation-class-attributes>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-173">For more information, see <xref:blazor/forms-validation#custom-validation-class-attributes>.</span></span>

### <a name="iasyncdisposable-support"></a><span data-ttu-id="ccd14-174">IAsyncDisposable 支持</span><span class="sxs-lookup"><span data-stu-id="ccd14-174">IAsyncDisposable support</span></span>

<span data-ttu-id="ccd14-175">Blazor 组件现在支持已分配资源的异步版本的 <xref:System.IAsyncDisposable> 接口。</span><span class="sxs-lookup"><span data-stu-id="ccd14-175">Blazor components now support the <xref:System.IAsyncDisposable> interface for the asynchronous release of allocated resources.</span></span>

### <a name="javascript-isolation-and-object-references"></a><span data-ttu-id="ccd14-176"> JavaScript 隔离和对象引用</span><span class="sxs-lookup"><span data-stu-id="ccd14-176">JavaScript isolation and object references</span></span>

<span data-ttu-id="ccd14-177">Blazor 在标准 [JavaScript 模块](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中启用 JavaScript 隔离。</span><span class="sxs-lookup"><span data-stu-id="ccd14-177">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="ccd14-178">有关详细信息，请参阅 <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-178">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

### <a name="form-components-support-display-name"></a><span data-ttu-id="ccd14-179">窗体组件支持显示名称</span><span class="sxs-lookup"><span data-stu-id="ccd14-179">Form components support display name</span></span>

<span data-ttu-id="ccd14-180">以下内置组件支持带有 `DisplayName` 参数的显示名称：</span><span class="sxs-lookup"><span data-stu-id="ccd14-180">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* `InputDate`
* `InputNumber`
* `InputSelect`

<span data-ttu-id="ccd14-181">有关详细信息，请参阅 <xref:blazor/forms-validation#display-name-support>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-181">For more information, see <xref:blazor/forms-validation#display-name-support>.</span></span>

### <a name="catch-all-route-parameters"></a><span data-ttu-id="ccd14-182">catch-all 路由参数</span><span class="sxs-lookup"><span data-stu-id="ccd14-182">Catch-all route parameters</span></span>

<span data-ttu-id="ccd14-183">组件支持可跨多个文件夹边界捕获路径的 catch-all 路由参数。</span><span class="sxs-lookup"><span data-stu-id="ccd14-183">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="ccd14-184">有关详细信息，请参阅 <xref:blazor/fundamentals/routing#catch-all-route-parameters>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-184">For more information, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="debugging-improvements"></a><span data-ttu-id="ccd14-185">调试改进</span><span class="sxs-lookup"><span data-stu-id="ccd14-185">Debugging improvements</span></span>

<span data-ttu-id="ccd14-186">调试 Blazor WebAssembly 应用在 ASP.NET Core 5.0 中得到了改进。</span><span class="sxs-lookup"><span data-stu-id="ccd14-186">Debugging Blazor WebAssembly apps is improved in ASP.NET Core 5.0.</span></span> <span data-ttu-id="ccd14-187">此外，Visual Studio for Mac 上现在也支持调试。</span><span class="sxs-lookup"><span data-stu-id="ccd14-187">Additionally, debugging is now supported on Visual Studio for Mac.</span></span> <span data-ttu-id="ccd14-188">有关详细信息，请参阅 <xref:blazor/debug>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-188">For more information, see <xref:blazor/debug>.</span></span>

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a><span data-ttu-id="ccd14-189">Microsoft Identity v2.0 和 MSAL v2.0</span><span class="sxs-lookup"><span data-stu-id="ccd14-189">Microsoft Identity v2.0 and MSAL v2.0</span></span>

<span data-ttu-id="ccd14-190">Blazor 安全性现在使用 Microsoft Identity v2.0（[`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) 和 [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)）和 MSAL v2.0。</span><span class="sxs-lookup"><span data-stu-id="ccd14-190">Blazor security now uses Microsoft Identity v2.0 ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) and [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) and MSAL v2.0.</span></span> <span data-ttu-id="ccd14-191">有关详细信息，请参阅[Blazor 安全性和 Identity 节点](xref:blazor/security/index)中的主题。</span><span class="sxs-lookup"><span data-stu-id="ccd14-191">For more information, see the topics in the [Blazor Security and Identity node](xref:blazor/security/index).</span></span>

### <a name="protected-browser-storage-for-no-locblazor-server"></a><span data-ttu-id="ccd14-192">Blazor Server 受保护的浏览器存储</span><span class="sxs-lookup"><span data-stu-id="ccd14-192">Protected Browser Storage for Blazor Server</span></span>

<span data-ttu-id="ccd14-193">Blazor Server 应用现在可以使用内置支持在浏览器中存储应用状态，这已受到保护，无法使用 ASP.NET Core 数据保护进行篡改。</span><span class="sxs-lookup"><span data-stu-id="ccd14-193">Blazor Server apps can now use built-in support for storing app state in the browser that has been protected from tampering using ASP.NET Core data protection.</span></span> <span data-ttu-id="ccd14-194">数据可以存储在本地浏览器存储或会话存储中。</span><span class="sxs-lookup"><span data-stu-id="ccd14-194">Data can be stored in either local browser storage or session storage.</span></span> <span data-ttu-id="ccd14-195">有关详细信息，请参阅 <xref:blazor/state-management>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-195">For more information, see <xref:blazor/state-management>.</span></span>

### <a name="no-locblazor-webassembly-prerendering"></a><span data-ttu-id="ccd14-196">Blazor WebAssembly 预呈现</span><span class="sxs-lookup"><span data-stu-id="ccd14-196">Blazor WebAssembly prerendering</span></span>

<span data-ttu-id="ccd14-197">跨托管模型改进了组件集成，Blazor WebAssembly 应用现在可以在服务器上预呈现输出。</span><span class="sxs-lookup"><span data-stu-id="ccd14-197">Component integration is improved across hosting models, and Blazor WebAssembly apps can now prerender output on the server.</span></span> <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a><span data-ttu-id="ccd14-198">剪裁/链接改进</span><span class="sxs-lookup"><span data-stu-id="ccd14-198">Trimming/linking improvements</span></span>

<span data-ttu-id="ccd14-199">Blazor WebAssembly 在生成期间执行中间语言 (IL) 剪裁/链接，以从应用的输出程序集中剪裁不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="ccd14-199">Blazor WebAssembly performs Intermediate Language (IL) trimming/linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="ccd14-200">随着 ASP.NET Core 5.0 的发布，Blazor WebAssembly 通过其他配置选项来执行改进的剪裁。</span><span class="sxs-lookup"><span data-stu-id="ccd14-200">With the release of ASP.NET Core 5.0, Blazor WebAssembly performs improved trimming with additional configuration options.</span></span> <span data-ttu-id="ccd14-201">有关详细信息，请参阅 <xref:blazor/host-and-deploy/configure-trimmer> 和[剪裁选项](/dotnet/core/deploying/trimming-options)。</span><span class="sxs-lookup"><span data-stu-id="ccd14-201">For more information, see <xref:blazor/host-and-deploy/configure-trimmer> and [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

### <a name="browser-compatibility-analyzer"></a><span data-ttu-id="ccd14-202">浏览器兼容性分析器</span><span class="sxs-lookup"><span data-stu-id="ccd14-202">Browser compatibility analyzer</span></span>

<span data-ttu-id="ccd14-203">Blazor WebAssembly 应用面向整个 .NET API 外围应用，但由于浏览器沙盒约束，并非所有 .NET API 在 WebAssembly 上都受支持。</span><span class="sxs-lookup"><span data-stu-id="ccd14-203">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="ccd14-204">在 WebAssembly 上运行时，不支持的 API 将引发 <xref:System.PlatformNotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-204">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="ccd14-205">当应用使用应用目标平台不支持的 API 时，平台兼容性分析器会向开发人员发出警告。</span><span class="sxs-lookup"><span data-stu-id="ccd14-205">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="ccd14-206">有关详细信息，请参阅 <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-206">For more information, see <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="ccd14-207">延迟加载程序集</span><span class="sxs-lookup"><span data-stu-id="ccd14-207">Lazy load assemblies</span></span>

<span data-ttu-id="ccd14-208">通过推迟某些应用程序程序集的加载，直到需要时才加载，来提高 Blazor WebAssembly 应用启动性能。</span><span class="sxs-lookup"><span data-stu-id="ccd14-208">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they're required.</span></span> <span data-ttu-id="ccd14-209">有关详细信息，请参阅 <xref:blazor/webassembly-lazy-load-assemblies>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-209">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="updated-globalization-support"></a><span data-ttu-id="ccd14-210">已更新的全球化支持</span><span class="sxs-lookup"><span data-stu-id="ccd14-210">Updated globalization support</span></span>

<span data-ttu-id="ccd14-211">全球化支持适用于基于 International Components for Unicode (ICU) 的 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="ccd14-211">Globalization support is available for Blazor WebAssembly based on International Components for Unicode (ICU).</span></span> <span data-ttu-id="ccd14-212">有关详细信息，请参阅 <xref:blazor/globalization-localization>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-212">For more information, see <xref:blazor/globalization-localization>.</span></span>

## <a name="grpc"></a><span data-ttu-id="ccd14-213">gRPC</span><span class="sxs-lookup"><span data-stu-id="ccd14-213">gRPC</span></span>

<span data-ttu-id="ccd14-214">在 [gRPC](https://grpc.io/) 中进行了许多性能改进。</span><span class="sxs-lookup"><span data-stu-id="ccd14-214">Many preformance improvements have been made in [gRPC](https://grpc.io/).</span></span> <span data-ttu-id="ccd14-215">有关详细信息，请参阅 [.NET 5 中的 gRPC 性能改进](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/)。</span><span class="sxs-lookup"><span data-stu-id="ccd14-215">For more information, see [gRPC performance improvements in .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span></span>

<span data-ttu-id="ccd14-216">有关 gRPC 的详细信息，请参阅 <xref:grpc/index>。</span><span class="sxs-lookup"><span data-stu-id="ccd14-216">For more gRPC information, see <xref:grpc/index>.</span></span>

## SignalR

### <a name="no-locsignalr-hub-filters"></a><span data-ttu-id="ccd14-217">SignalR 中心筛选器</span><span class="sxs-lookup"><span data-stu-id="ccd14-217">SignalR Hub filters</span></span>

<span data-ttu-id="ccd14-218">SignalR 中心筛选器（在 ASP.NET SignalR 中称为“中心管道”）是一项功能，它允许代码在调用中心方法之前和之后运行。</span><span class="sxs-lookup"><span data-stu-id="ccd14-218">SignalR Hub filters, called Hub pipelines in ASP.NET SignalR, is a feature that allows code to run before and after Hub methods are called.</span></span> <span data-ttu-id="ccd14-219">在调用中心方法之前和之后运行代码类似于中间件在 HTTP 请求之前和之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="ccd14-219">Running code before and after Hub methods are called is similar to how middleware has the ability to run code before and after an HTTP request.</span></span> <span data-ttu-id="ccd14-220">常见用途包括日志记录、错误处理和参数验证。</span><span class="sxs-lookup"><span data-stu-id="ccd14-220">Common uses include logging, error handling, and argument validation.</span></span>

<span data-ttu-id="ccd14-221">有关详细信息，请参阅[在 ASP.NET Core SignalR 中使用中心筛选器](xref:signalr/hub-filters)。</span><span class="sxs-lookup"><span data-stu-id="ccd14-221">For more information, see [Use hub filters in ASP.NET Core SignalR](xref:signalr/hub-filters).</span></span>

### <a name="no-locsignalr-parallel-hub-invocations"></a><span data-ttu-id="ccd14-222">SignalR 并行中心调用</span><span class="sxs-lookup"><span data-stu-id="ccd14-222">SignalR parallel hub invocations</span></span>

<span data-ttu-id="ccd14-223">ASP.NET Core SignalR 现在能够处理并行中心调用。</span><span class="sxs-lookup"><span data-stu-id="ccd14-223">ASP.NET Core SignalR is now capable of handling parallel hub invocations.</span></span> <span data-ttu-id="ccd14-224">可以更改默认行为，以允许客户端一次调用多个中心方法：</span><span class="sxs-lookup"><span data-stu-id="ccd14-224">The default behavior can be changed to allow clients to invoke more than one hub method at a time:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a><span data-ttu-id="ccd14-225">在 SignalR Java 客户端中添加了 Messagepack 支持</span><span class="sxs-lookup"><span data-stu-id="ccd14-225">Added Messagepack support in SignalR Java client</span></span>

<span data-ttu-id="ccd14-226">新包 ([com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack)) 将 MessagePack 支持添加到 SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="ccd14-226">A new package, [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), adds MessagePack support to the SignalR Java client.</span></span> <span data-ttu-id="ccd14-227">若要使用 MessagePack 中心协议，请将 `.withHubProtocol(new MessagePackHubProtocol())` 添加到连接生成器：</span><span class="sxs-lookup"><span data-stu-id="ccd14-227">To use the MessagePack hub protocol, add `.withHubProtocol(new MessagePackHubProtocol())` to the connection builder:</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* <span data-ttu-id="ccd14-228">通过配置可重载的终结点：Kestrel 可以检测对传递到 [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) 的配置所做的更改，从现有终结点取消绑定并绑定到新终结点，而无需在 `reloadOnChange` 参数 `true` 时重新启动应用程序。</span><span class="sxs-lookup"><span data-stu-id="ccd14-228">Reloadable endpoints via configuration: Kestrel can detect changes to configuration passed to [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) and unbind from existing endpoints and bind to new endpoints without requiring an application restart when the `reloadOnChange` parameter is `true`.</span></span> <span data-ttu-id="ccd14-229">默认情况下，在使用 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> 或 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 时，Kestrel 将绑定到启用了 `reloadOnChange` 的[“Kestrel”](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226)配置子节。</span><span class="sxs-lookup"><span data-stu-id="ccd14-229">By default when using <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> or <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, Kestrel binds to the ["Kestrel"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) configuration subsection with `reloadOnChange` enabled.</span></span> <span data-ttu-id="ccd14-230">手动调用 `KestrelServerOptions.Configure` 以获取可重载终结点时，应用必须传递 `reloadOnChange: true`。</span><span class="sxs-lookup"><span data-stu-id="ccd14-230">Apps must pass `reloadOnChange: true` when calling `KestrelServerOptions.Configure` manually to get reloadable endpoints.</span></span>
* <span data-ttu-id="ccd14-231">HTTP/2 响应标头改进。</span><span class="sxs-lookup"><span data-stu-id="ccd14-231">HTTP/2 response headers improvements.</span></span> <span data-ttu-id="ccd14-232">有关详细信息，请参阅下一部分中的[性能改进](#performance-improvements)。</span><span class="sxs-lookup"><span data-stu-id="ccd14-232">For more information, see [Performance improvements](#performance-improvements) in the next section.</span></span>
* <span data-ttu-id="ccd14-233">支持套接字传输中的其他终结点类型：添加到 <xref:System.Net.Sockets?displayProperty=nameWithType> 中引入的新 API，[Kestrel](xref:fundamentals/servers/kestrel) 中的套接字默认传输允许绑定到现有文件句柄和 Unix 域套接字。</span><span class="sxs-lookup"><span data-stu-id="ccd14-233">Support for additional endpoints types in the sockets transport: Adding to the new API introduced in <xref:System.Net.Sockets?displayProperty=nameWithType>, the sockets default transport in [Kestrel](xref:fundamentals/servers/kestrel) allows binding to both existing file handles and Unix domain sockets.</span></span> <span data-ttu-id="ccd14-234">如果支持绑定到现有文件句柄，则无需 `libuv` 传输即可使用现有 `Systemd` 集成。</span><span class="sxs-lookup"><span data-stu-id="ccd14-234">Support for binding to existing file handles enables using the existing `Systemd` integration without requiring the `libuv` transport.</span></span>
* <span data-ttu-id="ccd14-235">Kestrel 中的自定义标头解码：应用可以指定使用哪个 <xref:System.Text.Encoding> 来基于标头名称解释传入标头，而不是默认为 UTF-8。</span><span class="sxs-lookup"><span data-stu-id="ccd14-235">Custom header decoding in Kestrel: Apps can specify which <xref:System.Text.Encoding> to use to interpret incoming headers based on the header name instead of defaulting to UTF-8.</span></span> <span data-ttu-id="ccd14-236">在包含身份验证方案、帐户名和身份验证签名的字符串中设置</span><span class="sxs-lookup"><span data-stu-id="ccd14-236">Set the</span></span> <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> <span data-ttu-id="ccd14-237">`Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` 属性用于指定要使用的编码：</span><span class="sxs-lookup"><span data-stu-id="ccd14-237">`Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` property to specify which encoding to use:</span></span>
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a><span data-ttu-id="ccd14-238">通过配置的 Kestrel 终结点特定选项</span><span class="sxs-lookup"><span data-stu-id="ccd14-238">Kestrel endpoint-specific options via configuration</span></span>

<span data-ttu-id="ccd14-239">添加了通过[配置](xref:fundamentals/configuration/index)来配置 Kestrel 的终结点特定选项的支持。</span><span class="sxs-lookup"><span data-stu-id="ccd14-239">Support has been added for configuring Kestrel’s endpoint-specific options via [configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="ccd14-240">终结点特定的配置包括：</span><span class="sxs-lookup"><span data-stu-id="ccd14-240">The endpoint-specific configurations includes the:</span></span>

* <span data-ttu-id="ccd14-241">已使用 HTTP 协议</span><span class="sxs-lookup"><span data-stu-id="ccd14-241">HTTP protocols used</span></span>
* <span data-ttu-id="ccd14-242">已使用 TLS 协议</span><span class="sxs-lookup"><span data-stu-id="ccd14-242">TLS protocols used</span></span>
* <span data-ttu-id="ccd14-243">已选择证书</span><span class="sxs-lookup"><span data-stu-id="ccd14-243">Certificate selected</span></span>
* <span data-ttu-id="ccd14-244">客户端证书模式</span><span class="sxs-lookup"><span data-stu-id="ccd14-244">Client certificate mode</span></span>

<span data-ttu-id="ccd14-245">配置允许指定基于指定服务器名称选择的证书。</span><span class="sxs-lookup"><span data-stu-id="ccd14-245">Configuration allows specifying which certificate is selected based on the specified server name.</span></span> <span data-ttu-id="ccd14-246">服务器名称是客户端所指示的 TLS 协议的服务器名称指示 (SNI) 扩展的一部分。</span><span class="sxs-lookup"><span data-stu-id="ccd14-246">The server name is part of the Server Name Indication (SNI) extension to the TLS protocol as indicated by the client.</span></span> <span data-ttu-id="ccd14-247">Kestrel 的配置还支持在主机名中使用通配符前缀。</span><span class="sxs-lookup"><span data-stu-id="ccd14-247">Kestrel's configuration also supports a wildcard prefix in the host name.</span></span>

<span data-ttu-id="ccd14-248">下面的示例演示如何使用配置文件指定终结点特定的配置：</span><span class="sxs-lookup"><span data-stu-id="ccd14-248">The following example shows how to specify endpoint-specific using a configuration file:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

<span data-ttu-id="ccd14-249">服务器名称指示 (SNI) 是一种 TLS 扩展，可将虚拟域作为 SSL 协商的一部分包括在内。</span><span class="sxs-lookup"><span data-stu-id="ccd14-249">Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="ccd14-250">这实际上表示虚拟域名或主机名可用于标识网络终结点。</span><span class="sxs-lookup"><span data-stu-id="ccd14-250">What this effectively means is that the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="ccd14-251">性能改进</span><span class="sxs-lookup"><span data-stu-id="ccd14-251">Performance improvements</span></span>

### <a name="http2"></a><span data-ttu-id="ccd14-252">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ccd14-252">HTTP/2</span></span>

* <span data-ttu-id="ccd14-253">显著减少了 HTTP/2 代码路径中的分配。</span><span class="sxs-lookup"><span data-stu-id="ccd14-253">Significant reductions in allocations in the HTTP/2 code path.</span></span>
* <span data-ttu-id="ccd14-254">支持 [Kestrel](xref:fundamentals/servers/kestrel) 中的 HTTP/2 响应标头的 [HPack 动态压缩](https://tools.ietf.org/html/rfc7541)。</span><span class="sxs-lookup"><span data-stu-id="ccd14-254">Support for [HPack dynamic compression](https://tools.ietf.org/html/rfc7541) of HTTP/2 response headers in [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="ccd14-255">有关详细信息，请参阅[标头表大小](xref:fundamentals/servers/kestrel/options#header-table-size)和 [HPACK：HTTP/2 的静默杀手锏](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/)。</span><span class="sxs-lookup"><span data-stu-id="ccd14-255">For more information, see [Header table size](xref:fundamentals/servers/kestrel/options#header-table-size) and [HPACK: the silent killer (feature) of HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span></span>
* <span data-ttu-id="ccd14-256">发送 HTTP/2 PING 帧：HTTP/2 有一种机制，用于发送 PING 帧以确保空闲连接仍然正常工作。</span><span class="sxs-lookup"><span data-stu-id="ccd14-256">Sending HTTP/2 PING frames: HTTP/2 has a mechanism for sending PING frames to ensure an idle connection is still functional.</span></span> <span data-ttu-id="ccd14-257">当使用经常空闲但仅可间歇查看活动的长生存期流（例如，gRPC 流）时，确保可行连接特别有用。</span><span class="sxs-lookup"><span data-stu-id="ccd14-257">Ensuring a viable connection is especially useful when working with long-lived streams that are often idle but only intermittently see activity, for example, gRPC streams.</span></span> <span data-ttu-id="ccd14-258">应用可以通过对 <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions> 设置限制，在 [Kestrel](xref:fundamentals/servers/kestrel) 中发送定期 PING 帧：</span><span class="sxs-lookup"><span data-stu-id="ccd14-258">Apps can send periodic PING frames in [Kestrel](xref:fundamentals/servers/kestrel) by setting limits on <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions>:</span></span>

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a><span data-ttu-id="ccd14-259">容器</span><span class="sxs-lookup"><span data-stu-id="ccd14-259">Containers</span></span>

<span data-ttu-id="ccd14-260">在 .NET 5.0 之前，生成并发布用于 ASP.NET Core 应用的 Dockerfile 需要提取整个 .NET Core SDK 和 ASP.NET Core 映像。</span><span class="sxs-lookup"><span data-stu-id="ccd14-260">Prior to .NET 5.0, building and publishing a *Dockerfile* for an ASP.NET Core app required pulling the entire .NET Core SDK and the ASP.NET Core image.</span></span> <span data-ttu-id="ccd14-261">在此版本中，将减少提取 SDK 图像字节数，并大大消除为 ASP.NET Core 映像提取的字节数。</span><span class="sxs-lookup"><span data-stu-id="ccd14-261">With this release, pulling the SDK images bytes is reduced and the bytes pulled for the ASP.NET Core image is largely eliminated.</span></span> <span data-ttu-id="ccd14-262">有关详细信息，请参阅[此 GitHub 问题注释](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750)。</span><span class="sxs-lookup"><span data-stu-id="ccd14-262">For more information, see [this GitHub issue comment](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="ccd14-263">身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="ccd14-263">Authentication and authorization</span></span>

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a><span data-ttu-id="ccd14-264">使用 Microsoft.Identity.Web 的 Azure Active Directory 身份验证</span><span class="sxs-lookup"><span data-stu-id="ccd14-264">Azure Active Directory authentication with Microsoft.Identity.Web</span></span>

<span data-ttu-id="ccd14-265">ASP.NET Core 项目模板现在与 <xref:Microsoft.Identity.Web?displayProperty=fullName> 集成，以处理使用 [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) 的身份验证。</span><span class="sxs-lookup"><span data-stu-id="ccd14-265">The ASP.NET Core project templates now integrate with <xref:Microsoft.Identity.Web?displayProperty=fullName> to handle authentication with [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD).</span></span> <span data-ttu-id="ccd14-266">[Microsoft.Identity.Web package](https://www.nuget.org/packages/Microsoft.Identity.Web/) 提供：</span><span class="sxs-lookup"><span data-stu-id="ccd14-266">The [Microsoft.Identity.Web package](https://www.nuget.org/packages/Microsoft.Identity.Web/) provides:</span></span>

* <span data-ttu-id="ccd14-267">通过 Azure AD 进行身份验证的更好体验。</span><span class="sxs-lookup"><span data-stu-id="ccd14-267">A better experience for authentication through Azure AD.</span></span>
* <span data-ttu-id="ccd14-268">代表用户访问 Azure 资源的一种更简单方法，包括 [Microsoft Graph](/graph/overview)。</span><span class="sxs-lookup"><span data-stu-id="ccd14-268">An easier way to access Azure resources on behalf of your users, including [Microsoft Graph](/graph/overview).</span></span> <span data-ttu-id="ccd14-269">请参阅 [Microsoft.Identity.Web sample](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)，它从基本登录开始，通过多租户（使用 Azure API、使用 Microsoft Graph 并保护你自己的 API）前进。</span><span class="sxs-lookup"><span data-stu-id="ccd14-269">See the [Microsoft.Identity.Web sample](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), which starts with a basic login and advances through multi-tenancy, using Azure APIs, using Microsoft Graph, and protecting your own APIs.</span></span> <span data-ttu-id="ccd14-270">`Microsoft.Identity.Web` 与 .NET 5 一起提供。</span><span class="sxs-lookup"><span data-stu-id="ccd14-270">`Microsoft.Identity.Web` is available alongside .NET 5.</span></span>

### <a name="allow-anonymous-access-to-an-endpoint"></a><span data-ttu-id="ccd14-271">允许匿名访问终结点</span><span class="sxs-lookup"><span data-stu-id="ccd14-271">Allow anonymous access to an endpoint</span></span>

<span data-ttu-id="ccd14-272">`AllowAnonymous` 扩展方法允许匿名访问终结点：</span><span class="sxs-lookup"><span data-stu-id="ccd14-272">The `AllowAnonymous` extension method allows anonymous access to an endpoint:</span></span>

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a><span data-ttu-id="ccd14-273">自定义处理授权失败</span><span class="sxs-lookup"><span data-stu-id="ccd14-273">Custom handling of authorization failures</span></span>

<span data-ttu-id="ccd14-274">现在，使用由[授权](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A)[中间件](xref:fundamentals/middleware/index)调用的新 [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) 接口可以更轻松地自定义处理授权失败。</span><span class="sxs-lookup"><span data-stu-id="ccd14-274">Custom handling of authorization failures is now easier with the new [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) interface that is invoked by the [authorization](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="ccd14-275">默认实现保持不变，但可以在“依赖关系注入”中注册自定义处理程序，这允许基于授权失败的原因发出自定义 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="ccd14-275">The default implementation remains the same, but a custom handler can be registered in [Dependency injection, which allows custom HTTP responses based on why authorization failed.</span></span> <span data-ttu-id="ccd14-276">请参阅用于演示 `IAuthorizationMiddlewareResultHandler` 的使用情况的[此示例](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs)。</span><span class="sxs-lookup"><span data-stu-id="ccd14-276">See [this sample](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) that demonstrates usage of the `IAuthorizationMiddlewareResultHandler`.</span></span>

### <a name="authorization-when-using-endpoint-routing"></a><span data-ttu-id="ccd14-277">使用终结点路由时的授权</span><span class="sxs-lookup"><span data-stu-id="ccd14-277">Authorization when using endpoint routing</span></span>

<span data-ttu-id="ccd14-278">使用终结点路由时的授权现在会接收 `HttpContext` 而不是终结点实例。</span><span class="sxs-lookup"><span data-stu-id="ccd14-278">Authorization when using endpoint routing now receives the `HttpContext` rather than the endpoint instance.</span></span> <span data-ttu-id="ccd14-279">这允许授权中间件访问 `RouteData` 以及无法通过 <xref:Microsoft.AspNetCore.Http.Endpoint> 类访问的 `HttpContext` 的其他属性。</span><span class="sxs-lookup"><span data-stu-id="ccd14-279">This allows the authorization middleware to access the `RouteData` and other properties of the `HttpContext` that were not accessible though the <xref:Microsoft.AspNetCore.Http.Endpoint> class.</span></span> <span data-ttu-id="ccd14-280">可以使用 [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A) 从上下文中提取终结点。</span><span class="sxs-lookup"><span data-stu-id="ccd14-280">The endpoint can be fetched from the context using [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span></span>

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a><span data-ttu-id="ccd14-281">使用 Linux 上的 Kerberos 身份验证和 LDAP 的基于角色的访问控制</span><span class="sxs-lookup"><span data-stu-id="ccd14-281">Role-based access control with Kerberos authentication and LDAP on Linux</span></span>

<span data-ttu-id="ccd14-282">请参阅 [Kerberos 身份验证和基于角色的访问控制 (RBAC)](xref:security/authentication/windowsauth#rbac)</span><span class="sxs-lookup"><span data-stu-id="ccd14-282">See [Kerberos authentication and role-based access control (RBAC)](xref:security/authentication/windowsauth#rbac)</span></span>

## <a name="api-improvements"></a><span data-ttu-id="ccd14-283">API 改进</span><span class="sxs-lookup"><span data-stu-id="ccd14-283">API improvements</span></span>

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a><span data-ttu-id="ccd14-284">用于 HttpRequest 和 HttpResponse 的 JSON 扩展方法</span><span class="sxs-lookup"><span data-stu-id="ccd14-284">JSON extension methods for HttpRequest and HttpResponse</span></span>

<span data-ttu-id="ccd14-285">可以使用新的 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 和 `WriteAsJsonAsync` 扩展方法从 `HttpRequest` 和 `HttpResponse` 读取和写入 JSON 数据。</span><span class="sxs-lookup"><span data-stu-id="ccd14-285">JSON data can be read and written to from an `HttpRequest` and `HttpResponse` using the new <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> and `WriteAsJsonAsync` extension methods.</span></span> <span data-ttu-id="ccd14-286">这些扩展方法使用 [System.Text.Json](xref:System.Text.Json) 序列化程序来处理 JSON 数据。</span><span class="sxs-lookup"><span data-stu-id="ccd14-286">These extension methods use the [System.Text.Json](xref:System.Text.Json) serializer to handle the JSON data.</span></span> <span data-ttu-id="ccd14-287">新的 `HasJsonContentType` 扩展方法还可以检查请求是否具有 JSON 内容类型。</span><span class="sxs-lookup"><span data-stu-id="ccd14-287">The new `HasJsonContentType` extension method can also check if a request has a JSON content type.</span></span>

<span data-ttu-id="ccd14-288">JSON 扩展方法可与[终结点路由](xref:fundamentals/routing)结合使用，以编程方式创建 JSON API，我们称之为“路由到代码”\*_。</span><span class="sxs-lookup"><span data-stu-id="ccd14-288">The JSON extension methods can be combined with [endpoint routing](xref:fundamentals/routing) to create JSON APIs in a style of programming we call \***route to code** _.</span></span> <span data-ttu-id="ccd14-289">这是一个新选项，适用于想要以轻量级方式创建基本 JSON API 的开发人员。</span><span class="sxs-lookup"><span data-stu-id="ccd14-289">It is a new option for developers who want to create basic JSON APIs in a lightweight way.</span></span> <span data-ttu-id="ccd14-290">例如，只有少量终结点的 Web 应用可能选择使用路由到代码，而不是 ASP.NET Core MVC 的全部功能：</span><span class="sxs-lookup"><span data-stu-id="ccd14-290">For example, a web app that has only a handful of endpoints might choose to use route to code rather than the full functionality of ASP.NET Core MVC:</span></span>

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

<span data-ttu-id="ccd14-291">有关新 JSON 扩展方法以及路由到代码的详细信息，请参阅[此 .NET show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code)。</span><span class="sxs-lookup"><span data-stu-id="ccd14-291">For more information on the new JSON extension methods and route to code, see [this .NET show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span></span>

### <a name="systemdiagnosticsactivity"></a><span data-ttu-id="ccd14-292">System.Diagnostics.Activity</span><span class="sxs-lookup"><span data-stu-id="ccd14-292">System.Diagnostics.Activity</span></span>

<span data-ttu-id="ccd14-293"><xref:System.Diagnostics.Activity?displayProperty=fullName> 的默认格式现在默认为 W3C 格式。</span><span class="sxs-lookup"><span data-stu-id="ccd14-293">The default format for <xref:System.Diagnostics.Activity?displayProperty=fullName> now defaults to the W3C format.</span></span> <span data-ttu-id="ccd14-294">默认情况下，这使得 ASP.NET Core 中的分布式跟踪支持可与更多框架进行互操作。</span><span class="sxs-lookup"><span data-stu-id="ccd14-294">This makes distributed tracing support in ASP.NET Core interoperable with more frameworks by default.</span></span>

### <a name="frombodyattribute"></a><span data-ttu-id="ccd14-295">FromBodyAttribute</span><span class="sxs-lookup"><span data-stu-id="ccd14-295">FromBodyAttribute</span></span>

<span data-ttu-id="ccd14-296"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> 现在支持配置一个允许将这些参数或属性视为可选的选项：</span><span class="sxs-lookup"><span data-stu-id="ccd14-296"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> now supports configuring an option that allows these parameters or properties to be considered optional:</span></span>

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                          MyModel model)
{
    ...
}
```

## <a name="miscellaneous-improvements"></a><span data-ttu-id="ccd14-297">其他改进</span><span class="sxs-lookup"><span data-stu-id="ccd14-297">Miscellaneous improvements</span></span>

<span data-ttu-id="ccd14-298">我们已开始将[可以为 null 的注释](/dotnet/csharp/nullable-references#attributes-describe-apis)应用到 ASP.NET Core 程序集。</span><span class="sxs-lookup"><span data-stu-id="ccd14-298">We’ve started applying [nullable annotations](/dotnet/csharp/nullable-references#attributes-describe-apis) to ASP.NET Core assemblies.</span></span> <span data-ttu-id="ccd14-299">我们计划为 .NET 5 Framework 的大多数常见公共 API 图面添加批注。</span><span class="sxs-lookup"><span data-stu-id="ccd14-299">We plan to annotate most of the common public API surface of the .NET 5 framework.</span></span> <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a><span data-ttu-id="ccd14-300">控制 Startup 类激活</span><span class="sxs-lookup"><span data-stu-id="ccd14-300">Control Startup class activation</span></span>

<span data-ttu-id="ccd14-301">添加了额外的 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> 重载，使应用能够提供工厂方法来控制 `Startup` 类激活。</span><span class="sxs-lookup"><span data-stu-id="ccd14-301">An additional <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> overload has been added that lets an app provide a factory method for controlling `Startup` class activation.</span></span> <span data-ttu-id="ccd14-302">控制 `Startup` 类激活有助于将附加参数传递给与主机一起初始化的 `Startup`：</span><span class="sxs-lookup"><span data-stu-id="ccd14-302">Controlling `Startup` class activation is useful to pass additional parameters to `Startup` that are initialized along with the host:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a><span data-ttu-id="ccd14-303">使用 dotnet watch 自动刷新</span><span class="sxs-lookup"><span data-stu-id="ccd14-303">Auto refresh with dotnet watch</span></span>

<span data-ttu-id="ccd14-304">在 .NET 5 中，对 ASP.NET Core 项目运行 [dotnet watch](xref:tutorials/dotnet-watch) 将启动默认浏览器，并在对代码进行更改时自动刷新浏览器。</span><span class="sxs-lookup"><span data-stu-id="ccd14-304">In .NET 5, running [dotnet watch](xref:tutorials/dotnet-watch) on an ASP.NET Core project both launches the default browser and auto refreshes the browser as changes are made to the code.</span></span> <span data-ttu-id="ccd14-305">这意味着可以执行下列操作：</span><span class="sxs-lookup"><span data-stu-id="ccd14-305">This means you can:</span></span>

<span data-ttu-id="ccd14-306">_ 在文本编辑器中打开 ASP.NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="ccd14-306">_ Open an ASP.NET Core project in a text editor.</span></span>
* <span data-ttu-id="ccd14-307">运行 `dotnet watch`。</span><span class="sxs-lookup"><span data-stu-id="ccd14-307">Run `dotnet watch`.</span></span>
* <span data-ttu-id="ccd14-308">专注于代码更改，而工具处理应用的重新生成、重新启动和重新加载。</span><span class="sxs-lookup"><span data-stu-id="ccd14-308">Focus on the code changes while the tooling handles rebuilding, restarting, and reloading the app.</span></span>

### <a name="console-logger-formatter"></a><span data-ttu-id="ccd14-309">控制台记录器格式化程序</span><span class="sxs-lookup"><span data-stu-id="ccd14-309">Console Logger Formatter</span></span>

<span data-ttu-id="ccd14-310">已对 `Microsoft.Extensions.Logging` 库中的控制台日志提供程序进行了改进。</span><span class="sxs-lookup"><span data-stu-id="ccd14-310">Improvements have been made to the console log provider in the `Microsoft.Extensions.Logging` library.</span></span> <span data-ttu-id="ccd14-311">开发人员现在可以实现自定义 `ConsoleFormatter`，以对控制台输出的格式设置和着色进行完全控制。</span><span class="sxs-lookup"><span data-stu-id="ccd14-311">Developers can now implement a custom `ConsoleFormatter` to exercise complete control over formatting and colorization of the console output.</span></span> <span data-ttu-id="ccd14-312">格式化程序 API 通过实现 VT-100 转义序列的子集进行丰富的格式设置。</span><span class="sxs-lookup"><span data-stu-id="ccd14-312">The formatter APIs allow for rich formatting by implementing a subset of the VT-100 escape sequences.</span></span> <span data-ttu-id="ccd14-313">VT-100 受大多数新式终端支持。</span><span class="sxs-lookup"><span data-stu-id="ccd14-313">VT-100 is supported by most modern terminals.</span></span> <span data-ttu-id="ccd14-314">控制台记录器可以对不受支持的终端分析转义序列，使开发人员可以为所有终端创作单个格式化程序。</span><span class="sxs-lookup"><span data-stu-id="ccd14-314">The console logger can parse out escape sequences on unsupported terminals allowing developers to author a single formatter for all terminals.</span></span>

### <a name="json-console-logger"></a><span data-ttu-id="ccd14-315">JSON 控制台记录器</span><span class="sxs-lookup"><span data-stu-id="ccd14-315">JSON Console Logger</span></span>

<span data-ttu-id="ccd14-316">除了对自定义格式化程序的支持外，我们还添加了一个内置 JSON 格式化程序，用于向控制台发出结构化 JSON 日志。</span><span class="sxs-lookup"><span data-stu-id="ccd14-316">In addition to support for custom formatters, we’ve also added a built-in JSON formatter that emits structured JSON logs to the console.</span></span> <span data-ttu-id="ccd14-317">下面的代码演示如何从默认记录器切换到 JSON：</span><span class="sxs-lookup"><span data-stu-id="ccd14-317">The following code shows how to switch from the default logger to JSON:</span></span>

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

<span data-ttu-id="ccd14-318">发出到控制台的日志消息为 JSON 格式：</span><span class="sxs-lookup"><span data-stu-id="ccd14-318">Log messages emitted to the console are JSON formatted:</span></span>

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
