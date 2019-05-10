---
title: 使用 ASP.NET Core 中的应用程序模型
author: ardalis
description: 了解如何读取和控制应用程序模型，从而修改 MVC 元素在 ASP.NET Core 中的行为。
ms.author: riande
ms.date: 10/14/2016
uid: mvc/controllers/application-model
ms.openlocfilehash: 6b0591a877c0d82e0ee6ab002eb6a6650753677b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890992"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a><span data-ttu-id="149d1-103">使用 ASP.NET Core 中的应用程序模型</span><span class="sxs-lookup"><span data-stu-id="149d1-103">Work with the application model in ASP.NET Core</span></span>

<span data-ttu-id="149d1-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="149d1-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="149d1-105">ASP.NET Core MVC 会定义一个*应用程序模型*，用于表示 MVC 应用的各个组件。</span><span class="sxs-lookup"><span data-stu-id="149d1-105">ASP.NET Core MVC defines an *application model* representing the components of an MVC app.</span></span> <span data-ttu-id="149d1-106">通过读取和处理此模型可修改 MVC 元素的行为方式。</span><span class="sxs-lookup"><span data-stu-id="149d1-106">You can read and manipulate this model to modify how MVC elements behave.</span></span> <span data-ttu-id="149d1-107">默认情况下，MVC 遵循特定的约定，以确定将哪些类视作控制器，这些类上的哪些方法是操作，以及参数和路由的行为方式。</span><span class="sxs-lookup"><span data-stu-id="149d1-107">By default, MVC follows certain conventions to determine which classes are considered to be controllers, which methods on those classes are actions, and how parameters and routing behave.</span></span> <span data-ttu-id="149d1-108">你可以自定义此行为以满足应用的需要，方法如下：创建自己的约定，并将它们应用于全局或作为属性应用。</span><span class="sxs-lookup"><span data-stu-id="149d1-108">You can customize this behavior to suit your app's needs by creating your own conventions and applying them globally or as attributes.</span></span>

## <a name="models-and-providers"></a><span data-ttu-id="149d1-109">模型和提供程序</span><span class="sxs-lookup"><span data-stu-id="149d1-109">Models and Providers</span></span>

<span data-ttu-id="149d1-110">ASP.NET Core MVC 应用程序模型包括用于描述 MVC 应用程序的抽象接口和具体实现类。</span><span class="sxs-lookup"><span data-stu-id="149d1-110">The ASP.NET Core MVC application model include both abstract interfaces and concrete implementation classes that describe an MVC application.</span></span> <span data-ttu-id="149d1-111">此模型是 MVC 根据默认约定发现应用的控制器、操作、操作参数、路由和筛选器的结果。</span><span class="sxs-lookup"><span data-stu-id="149d1-111">This model is the result of MVC discovering the app's controllers, actions, action parameters, routes, and filters according to default conventions.</span></span> <span data-ttu-id="149d1-112">通过使用应用程序模型，可以修改应用以遵循与默认 MVC 行为不同的约定。</span><span class="sxs-lookup"><span data-stu-id="149d1-112">By working with the application model, you can modify your app to follow different conventions from the default MVC behavior.</span></span> <span data-ttu-id="149d1-113">参数、名称、路由和筛选器都用作操作和控制器的配置数据。</span><span class="sxs-lookup"><span data-stu-id="149d1-113">The parameters, names, routes, and filters are all used as configuration data for actions and controllers.</span></span>

<span data-ttu-id="149d1-114">ASP.NET Core MVC 应用程序模型具有以下结构：</span><span class="sxs-lookup"><span data-stu-id="149d1-114">The ASP.NET Core MVC Application Model has the following structure:</span></span>

* <span data-ttu-id="149d1-115">ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="149d1-115">ApplicationModel</span></span>
  * <span data-ttu-id="149d1-116">控制器 (ControllerModel)</span><span class="sxs-lookup"><span data-stu-id="149d1-116">Controllers (ControllerModel)</span></span>
    * <span data-ttu-id="149d1-117">操作 (ActionModel)</span><span class="sxs-lookup"><span data-stu-id="149d1-117">Actions (ActionModel)</span></span>
      * <span data-ttu-id="149d1-118">参数 (ParameterModel)</span><span class="sxs-lookup"><span data-stu-id="149d1-118">Parameters (ParameterModel)</span></span>

<span data-ttu-id="149d1-119">该模型的每个级别都有权访问公用 `Properties` 集合，层次结构中的较低级别可以访问和覆盖由较高级别设置的属性值。</span><span class="sxs-lookup"><span data-stu-id="149d1-119">Each level of the model has access to a common `Properties` collection, and lower levels can access and overwrite property values set by higher levels in the hierarchy.</span></span> <span data-ttu-id="149d1-120">创建操作时，属性保存到 `ActionDescriptor.Properties` 中。</span><span class="sxs-lookup"><span data-stu-id="149d1-120">The properties are persisted to the `ActionDescriptor.Properties` when the actions are created.</span></span> <span data-ttu-id="149d1-121">之后，当处理请求时，可通过 `ActionContext.ActionDescriptor.Properties` 访问某个约定添加或修改的任何属性。</span><span class="sxs-lookup"><span data-stu-id="149d1-121">Then when a request is being handled, any properties a convention added or modified can be accessed through `ActionContext.ActionDescriptor.Properties`.</span></span> <span data-ttu-id="149d1-122">若要基于每项操作对筛选器、模型绑定器等进行配置，使用属性不失为一个好办法。</span><span class="sxs-lookup"><span data-stu-id="149d1-122">Using properties is a great way to configure your filters, model binders, etc. on a per-action basis.</span></span>

> [!NOTE]
> <span data-ttu-id="149d1-123">一旦完成应用启动，`ActionDescriptor.Properties` 集合就不再是线程安全的（针对写入）。</span><span class="sxs-lookup"><span data-stu-id="149d1-123">The `ActionDescriptor.Properties` collection isn't thread safe (for writes) once app startup has finished.</span></span> <span data-ttu-id="149d1-124">约定是将数据安全添加到此集合的最佳方式。</span><span class="sxs-lookup"><span data-stu-id="149d1-124">Conventions are the best way to safely add data to this collection.</span></span>

### <a name="iapplicationmodelprovider"></a><span data-ttu-id="149d1-125">IApplicationModelProvider</span><span class="sxs-lookup"><span data-stu-id="149d1-125">IApplicationModelProvider</span></span>

<span data-ttu-id="149d1-126">ASP.NET Core MVC 使用提供程序模式（由 [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) 接口定义）加载应用程序模型。</span><span class="sxs-lookup"><span data-stu-id="149d1-126">ASP.NET Core MVC loads the application model using a provider pattern, defined by the [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) interface.</span></span> <span data-ttu-id="149d1-127">此部分介绍此提供程序的工作原理的一些内部实现细节。</span><span class="sxs-lookup"><span data-stu-id="149d1-127">This section covers some of the internal implementation details of how this provider functions.</span></span> <span data-ttu-id="149d1-128">这是一项高级主题 — 利用应用程序模型的大多数应用应使用约定来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="149d1-128">This is an advanced topic - most apps that leverage the application model should do so by working with conventions.</span></span>

<span data-ttu-id="149d1-129">`IApplicationModelProvider` 接口的实现相互“包装”，每个实现都基于其 `Order` 属性以升序调用 `OnProvidersExecuting`。</span><span class="sxs-lookup"><span data-stu-id="149d1-129">Implementations of the `IApplicationModelProvider` interface "wrap" one another, with each implementation calling `OnProvidersExecuting` in ascending order based on its `Order` property.</span></span> <span data-ttu-id="149d1-130">然后，按相反的顺序调用 `OnProvidersExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="149d1-130">The `OnProvidersExecuted` method is then called in reverse order.</span></span> <span data-ttu-id="149d1-131">该框架定义了多个提供程序：</span><span class="sxs-lookup"><span data-stu-id="149d1-131">The framework defines several providers:</span></span>

<span data-ttu-id="149d1-132">首先 (`Order=-1000`)：</span><span class="sxs-lookup"><span data-stu-id="149d1-132">First (`Order=-1000`):</span></span>

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

<span data-ttu-id="149d1-133">然后 (`Order=-990`)：</span><span class="sxs-lookup"><span data-stu-id="149d1-133">Then (`Order=-990`):</span></span>

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> <span data-ttu-id="149d1-134">未定义具有相同 `Order` 值的两个提供程序的调用顺序，因此不应依赖此顺序。</span><span class="sxs-lookup"><span data-stu-id="149d1-134">The order in which two providers with the same value for `Order` are called is undefined, and therefore shouldn't be relied upon.</span></span>

> [!NOTE]
> <span data-ttu-id="149d1-135">`IApplicationModelProvider` 是一种高级概念，框架创建者可对其进行扩展。</span><span class="sxs-lookup"><span data-stu-id="149d1-135">`IApplicationModelProvider` is an advanced concept for framework authors to extend.</span></span> <span data-ttu-id="149d1-136">一般情况下，应用应使用约定，而框架应使用提供程序。</span><span class="sxs-lookup"><span data-stu-id="149d1-136">In general, apps should use conventions and frameworks should use providers.</span></span> <span data-ttu-id="149d1-137">主要不同之处在于提供程序始终先于约定运行。</span><span class="sxs-lookup"><span data-stu-id="149d1-137">The key distinction is that providers always run before conventions.</span></span>

<span data-ttu-id="149d1-138">`DefaultApplicationModelProvider` 建立了由 ASP.NET Core MVC 使用的许多默认行为。</span><span class="sxs-lookup"><span data-stu-id="149d1-138">The `DefaultApplicationModelProvider` establishes many of the default behaviors used by ASP.NET Core MVC.</span></span> <span data-ttu-id="149d1-139">其职责包括：</span><span class="sxs-lookup"><span data-stu-id="149d1-139">Its responsibilities include:</span></span>

* <span data-ttu-id="149d1-140">将全局筛选器添加到上下文</span><span class="sxs-lookup"><span data-stu-id="149d1-140">Adding global filters to the context</span></span>
* <span data-ttu-id="149d1-141">将控制器添加到上下文</span><span class="sxs-lookup"><span data-stu-id="149d1-141">Adding controllers to the context</span></span>
* <span data-ttu-id="149d1-142">将公共控制器方法作为操作添加</span><span class="sxs-lookup"><span data-stu-id="149d1-142">Adding public controller methods as actions</span></span>
* <span data-ttu-id="149d1-143">将操作方法参数添加到上下文</span><span class="sxs-lookup"><span data-stu-id="149d1-143">Adding action method parameters to the context</span></span>
* <span data-ttu-id="149d1-144">应用路由和其他属性</span><span class="sxs-lookup"><span data-stu-id="149d1-144">Applying route and other attributes</span></span>

<span data-ttu-id="149d1-145">某些内置行为由 `DefaultApplicationModelProvider` 实现。</span><span class="sxs-lookup"><span data-stu-id="149d1-145">Some built-in behaviors are implemented by the `DefaultApplicationModelProvider`.</span></span> <span data-ttu-id="149d1-146">此提供程序负责构造 [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel)，后者又引用 [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ActionModel)、[`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) 和 [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ParameterModel) 实例。</span><span class="sxs-lookup"><span data-stu-id="149d1-146">This provider is responsible for constructing the [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), which in turn references [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ActionModel), [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), and [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ParameterModel) instances.</span></span> <span data-ttu-id="149d1-147">`DefaultApplicationModelProvider` 类是一个内部框架实现细节，未来将对其进行更改。</span><span class="sxs-lookup"><span data-stu-id="149d1-147">The `DefaultApplicationModelProvider` class is an internal framework implementation detail that can and will change in the future.</span></span> 

<span data-ttu-id="149d1-148">`AuthorizationApplicationModelProvider` 负责应用与 `AuthorizeFilter` 和 `AllowAnonymousFilter` 属性关联的行为。</span><span class="sxs-lookup"><span data-stu-id="149d1-148">The `AuthorizationApplicationModelProvider` is responsible for applying the behavior associated with the `AuthorizeFilter` and `AllowAnonymousFilter` attributes.</span></span> <span data-ttu-id="149d1-149">[详细了解这些属性](xref:security/authorization/simple)。</span><span class="sxs-lookup"><span data-stu-id="149d1-149">[Learn more about these attributes](xref:security/authorization/simple).</span></span>

<span data-ttu-id="149d1-150">`CorsApplicationModelProvider` 可实现与 `IEnableCorsAttribute`、`IDisableCorsAttribute` 和 `DisableCorsAuthorizationFilter` 关联的行为。</span><span class="sxs-lookup"><span data-stu-id="149d1-150">The `CorsApplicationModelProvider` implements behavior associated with the `IEnableCorsAttribute` and `IDisableCorsAttribute`, and the `DisableCorsAuthorizationFilter`.</span></span> <span data-ttu-id="149d1-151">[详细了解 CORS](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="149d1-151">[Learn more about CORS](xref:security/cors).</span></span>

## <a name="conventions"></a><span data-ttu-id="149d1-152">约定</span><span class="sxs-lookup"><span data-stu-id="149d1-152">Conventions</span></span>

<span data-ttu-id="149d1-153">应用程序模型定义了约定抽象，通过约定抽象来自定义模型行为比重写整个模型或提供程序更简单。</span><span class="sxs-lookup"><span data-stu-id="149d1-153">The application model defines convention abstractions that provide a simpler way to customize the behavior of the models than overriding the entire model or provider.</span></span> <span data-ttu-id="149d1-154">建议使用这些抽象来修改应用的行为。</span><span class="sxs-lookup"><span data-stu-id="149d1-154">These abstractions are the recommended way to modify your app's behavior.</span></span> <span data-ttu-id="149d1-155">通过使用约定，可以编写能动态应用自定义项的代码。</span><span class="sxs-lookup"><span data-stu-id="149d1-155">Conventions provide a way for you to write code that will dynamically apply customizations.</span></span> <span data-ttu-id="149d1-156">使用[筛选器](xref:mvc/controllers/filters)可以修改框架的行为，而利用自定义项可以控制整个应用连接在一起的方式。</span><span class="sxs-lookup"><span data-stu-id="149d1-156">While [filters](xref:mvc/controllers/filters) provide a means of modifying the framework's behavior, customizations let you control how the whole app is wired together.</span></span>

<span data-ttu-id="149d1-157">可用约定如下：</span><span class="sxs-lookup"><span data-stu-id="149d1-157">The following conventions are available:</span></span>

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

<span data-ttu-id="149d1-158">可通过以下方式应用约定：将它们添加到 MVC 选项，或实现 `Attribute` 并将它们应用于控制器、操作或操作参数（类似于 [`Filters`](xref:mvc/controllers/filters)）。</span><span class="sxs-lookup"><span data-stu-id="149d1-158">Conventions are applied by adding them to MVC options or by implementing `Attribute`s and applying them to controllers, actions, or action parameters (similar to [`Filters`](xref:mvc/controllers/filters)).</span></span> <span data-ttu-id="149d1-159">与筛选器不同的是，约定仅在应用启动时执行，而不作为每个请求的一部分执行。</span><span class="sxs-lookup"><span data-stu-id="149d1-159">Unlike filters, conventions are only executed when the app is starting, not as part of each request.</span></span>

### <a name="sample-modifying-the-applicationmodel"></a><span data-ttu-id="149d1-160">示例:修改 ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="149d1-160">Sample: Modifying the ApplicationModel</span></span>

<span data-ttu-id="149d1-161">以下约定用于向应用程序模型添加属性。</span><span class="sxs-lookup"><span data-stu-id="149d1-161">The following convention is used to add a property to the application model.</span></span> 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

<span data-ttu-id="149d1-162">当在 `Startup` 的 `ConfigureServices` 中添加 MVC 时，应用程序模型约定作为选项应用。</span><span class="sxs-lookup"><span data-stu-id="149d1-162">Application model conventions are applied as options when MVC is added in `ConfigureServices` in `Startup`.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

<span data-ttu-id="149d1-163">可从控制器操作内的 `ActionDescriptor` 属性集合中访问属性：</span><span class="sxs-lookup"><span data-stu-id="149d1-163">Properties are accessible from the `ActionDescriptor` properties collection within controller actions:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a><span data-ttu-id="149d1-164">示例:修改 ControllerModel 说明</span><span class="sxs-lookup"><span data-stu-id="149d1-164">Sample: Modifying the ControllerModel Description</span></span>

<span data-ttu-id="149d1-165">与上一个示例一样，也可以修改控制器模型，以包含自定义属性。</span><span class="sxs-lookup"><span data-stu-id="149d1-165">As in the previous example, the controller model can also be modified to include custom properties.</span></span> <span data-ttu-id="149d1-166">这些属性将覆盖应用程序模型中指定的具有相同名称的现有属性。</span><span class="sxs-lookup"><span data-stu-id="149d1-166">These will override existing properties with the same name specified in the application model.</span></span> <span data-ttu-id="149d1-167">以下约定属性可在控制器级别添加说明：</span><span class="sxs-lookup"><span data-stu-id="149d1-167">The following convention attribute adds a description at the controller level:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

<span data-ttu-id="149d1-168">此约定在控制器上作为属性应用。</span><span class="sxs-lookup"><span data-stu-id="149d1-168">This convention is applied as an attribute on a controller.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

<span data-ttu-id="149d1-169">访问“description”属性的方式与前面示例中一样。</span><span class="sxs-lookup"><span data-stu-id="149d1-169">The "description" property is accessed in the same manner as in previous examples.</span></span>

### <a name="sample-modifying-the-actionmodel-description"></a><span data-ttu-id="149d1-170">示例:修改 ActionModel 说明</span><span class="sxs-lookup"><span data-stu-id="149d1-170">Sample: Modifying the ActionModel Description</span></span>

<span data-ttu-id="149d1-171">可向各项操作应用不同的属性约定，并覆盖已在应用程序或控制器级别应用的行为。</span><span class="sxs-lookup"><span data-stu-id="149d1-171">A separate attribute convention can be applied to individual actions, overriding behavior already applied at the application or controller level.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

<span data-ttu-id="149d1-172">通过将此约定应用于上一示例的控制器中的某项操作，演示了它如何覆盖控制器级别的约定：</span><span class="sxs-lookup"><span data-stu-id="149d1-172">Applying this to an action within the previous example's controller demonstrates how it overrides the controller-level convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a><span data-ttu-id="149d1-173">示例:修改 ParameterModel</span><span class="sxs-lookup"><span data-stu-id="149d1-173">Sample: Modifying the ParameterModel</span></span>

<span data-ttu-id="149d1-174">可将以下约定应用于操作参数，以修改其 `BindingInfo`。</span><span class="sxs-lookup"><span data-stu-id="149d1-174">The following convention can be applied to action parameters to modify their `BindingInfo`.</span></span> <span data-ttu-id="149d1-175">以下约定要求参数为路由参数；忽略其他可能的绑定源（比如查询字符串值）。</span><span class="sxs-lookup"><span data-stu-id="149d1-175">The following convention requires that the parameter be a route parameter; other potential binding sources (such as query string values) are ignored.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

<span data-ttu-id="149d1-176">该属性可应用于任何操作参数：</span><span class="sxs-lookup"><span data-stu-id="149d1-176">The attribute may be applied to any action parameter:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a><span data-ttu-id="149d1-177">示例:修改 ActionModel 名称</span><span class="sxs-lookup"><span data-stu-id="149d1-177">Sample: Modifying the ActionModel Name</span></span>

<span data-ttu-id="149d1-178">以下约定可修改 `ActionModel`，以更新其应用到的操作的*名称*。</span><span class="sxs-lookup"><span data-stu-id="149d1-178">The following convention modifies the `ActionModel` to update the *name* of the action to which it's applied.</span></span> <span data-ttu-id="149d1-179">新名称以参数形式提供给该属性。</span><span class="sxs-lookup"><span data-stu-id="149d1-179">The new name is provided as a parameter to the attribute.</span></span> <span data-ttu-id="149d1-180">此新名称供路由使用，因此它将影响用于访问此操作方法的路由。</span><span class="sxs-lookup"><span data-stu-id="149d1-180">This new name is used by routing, so it will affect the route used to reach this action method.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

<span data-ttu-id="149d1-181">此属性应用于 `HomeController` 中的操作方法：</span><span class="sxs-lookup"><span data-stu-id="149d1-181">This attribute is applied to an action method in the `HomeController`:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

<span data-ttu-id="149d1-182">即使方法名称为 `SomeName`，该属性也会覆盖 MVC 使用该方法名称的约定，并将操作名称替换为 `MyCoolAction`。</span><span class="sxs-lookup"><span data-stu-id="149d1-182">Even though the method name is `SomeName`, the attribute overrides the MVC convention of using the method name and replaces the action name with `MyCoolAction`.</span></span> <span data-ttu-id="149d1-183">因此，用于访问此操作的路由为 `/Home/MyCoolAction`。</span><span class="sxs-lookup"><span data-stu-id="149d1-183">Thus, the route used to reach this action is `/Home/MyCoolAction`.</span></span>

> [!NOTE]
> <span data-ttu-id="149d1-184">此示例本质上与使用内置 [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) 属性相同。</span><span class="sxs-lookup"><span data-stu-id="149d1-184">This example is essentially the same as using the built-in [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) attribute.</span></span>

### <a name="sample-custom-routing-convention"></a><span data-ttu-id="149d1-185">示例:自定义路由约定</span><span class="sxs-lookup"><span data-stu-id="149d1-185">Sample: Custom Routing Convention</span></span>

<span data-ttu-id="149d1-186">可以使用 `IApplicationModelConvention` 来自定义路由的工作方式。</span><span class="sxs-lookup"><span data-stu-id="149d1-186">You can use an `IApplicationModelConvention` to customize how routing works.</span></span> <span data-ttu-id="149d1-187">例如，以下约定会将控制器的命名空间合并到其路由中，并将命名空间中的 `.` 替换为路由中的 `/`：</span><span class="sxs-lookup"><span data-stu-id="149d1-187">For example, the following convention will incorporate Controllers' namespaces into their routes, replacing `.` in the namespace with `/` in the route:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

<span data-ttu-id="149d1-188">该约定作为一个选项添加到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="149d1-188">The convention is added as an option in Startup.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> <span data-ttu-id="149d1-189">可以使用 `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));` 来访问 `MvcOptions`，以将约定添加到[中间件](xref:fundamentals/middleware/index)</span><span class="sxs-lookup"><span data-stu-id="149d1-189">You can add conventions to your [middleware](xref:fundamentals/middleware/index) by accessing `MvcOptions` using `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span></span>

<span data-ttu-id="149d1-190">此示例将此约定应用于未使用属性路由的路由，其中，控制器名称包含“Namespace”。</span><span class="sxs-lookup"><span data-stu-id="149d1-190">This sample applies this convention to routes that are not using attribute routing where the controller has  "Namespace" in its name.</span></span> <span data-ttu-id="149d1-191">以下控制器演示了此约定：</span><span class="sxs-lookup"><span data-stu-id="149d1-191">The following controller demonstrates this convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a><span data-ttu-id="149d1-192">应用程序模型在 WebApiCompatShim 中的使用</span><span class="sxs-lookup"><span data-stu-id="149d1-192">Application Model Usage in WebApiCompatShim</span></span>

<span data-ttu-id="149d1-193">ASP.NET Core MVC 使用一组不同于 ASP.NET Web API 2 的约定。</span><span class="sxs-lookup"><span data-stu-id="149d1-193">ASP.NET Core MVC uses a different set of conventions from ASP.NET Web API 2.</span></span> <span data-ttu-id="149d1-194">使用自定义约定，可以修改 ASP.NET Core MVC 应用的行为，使其与 Web API 应用保持一致。</span><span class="sxs-lookup"><span data-stu-id="149d1-194">Using custom conventions, you can modify an ASP.NET Core MVC app's behavior to be consistent with that of a Web API app.</span></span> <span data-ttu-id="149d1-195">Microsoft 附带了专用于此的 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/)。</span><span class="sxs-lookup"><span data-stu-id="149d1-195">Microsoft ships the [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) specifically for this purpose.</span></span>

> [!NOTE]
> <span data-ttu-id="149d1-196">详细了解[从 ASP.NET Web API 迁移](xref:migration/webapi)。</span><span class="sxs-lookup"><span data-stu-id="149d1-196">Learn more about [migration from ASP.NET Web API](xref:migration/webapi).</span></span>

<span data-ttu-id="149d1-197">若要使用 Web API Compatibility Shim，需将该包添加到项目中，然后通过调用 `Startup` 中的 `AddWebApiConventions`，将约定添加到 MVC：</span><span class="sxs-lookup"><span data-stu-id="149d1-197">To use the Web API Compatibility Shim, you need to add the package to your project and then add the conventions to MVC by calling `AddWebApiConventions` in `Startup`:</span></span>

```csharp
services.AddMvc().AddWebApiConventions();
```

<span data-ttu-id="149d1-198">该填充程序提供的约定仅适用于应用中已应用特定属性的部分。</span><span class="sxs-lookup"><span data-stu-id="149d1-198">The conventions provided by the shim are only applied to parts of the app that have had certain attributes applied to them.</span></span> <span data-ttu-id="149d1-199">以下四个属性用于控制哪些控制器应使用该填充程序的约定来修改自己的约定：</span><span class="sxs-lookup"><span data-stu-id="149d1-199">The following four attributes are used to control which controllers should have their conventions modified by the shim's conventions:</span></span>

* [<span data-ttu-id="149d1-200">UseWebApiActionConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="149d1-200">UseWebApiActionConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [<span data-ttu-id="149d1-201">UseWebApiOverloadingAttribute</span><span class="sxs-lookup"><span data-stu-id="149d1-201">UseWebApiOverloadingAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [<span data-ttu-id="149d1-202">UseWebApiParameterConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="149d1-202">UseWebApiParameterConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [<span data-ttu-id="149d1-203">UseWebApiRoutesAttribute</span><span class="sxs-lookup"><span data-stu-id="149d1-203">UseWebApiRoutesAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a><span data-ttu-id="149d1-204">操作约定</span><span class="sxs-lookup"><span data-stu-id="149d1-204">Action Conventions</span></span>

<span data-ttu-id="149d1-205">`UseWebApiActionConventionsAttribute` 用于根据名称将 HTTP 方法映射到操作（例如，`Get` 将映射到 `HttpGet`）。</span><span class="sxs-lookup"><span data-stu-id="149d1-205">The `UseWebApiActionConventionsAttribute` is used to map the HTTP method to actions based on their name (for instance, `Get` would map to `HttpGet`).</span></span> <span data-ttu-id="149d1-206">它仅适用于不使用属性路由的操作。</span><span class="sxs-lookup"><span data-stu-id="149d1-206">It only applies to actions that don't use attribute routing.</span></span>

### <a name="overloading"></a><span data-ttu-id="149d1-207">重载</span><span class="sxs-lookup"><span data-stu-id="149d1-207">Overloading</span></span>

<span data-ttu-id="149d1-208">`UseWebApiOverloadingAttribute` 用于应用 `WebApiOverloadingApplicationModelConvention` 约定。</span><span class="sxs-lookup"><span data-stu-id="149d1-208">The `UseWebApiOverloadingAttribute` is used to apply the `WebApiOverloadingApplicationModelConvention` convention.</span></span> <span data-ttu-id="149d1-209">此约定可向操作选择过程添加 `OverloadActionConstraint`，以将候选操作限制为其请求满足所有非可选参数的操作。</span><span class="sxs-lookup"><span data-stu-id="149d1-209">This convention adds an `OverloadActionConstraint` to the action selection process, which limits candidate actions to those for which the request satisfies all non-optional parameters.</span></span>

### <a name="parameter-conventions"></a><span data-ttu-id="149d1-210">参数约定</span><span class="sxs-lookup"><span data-stu-id="149d1-210">Parameter Conventions</span></span>

<span data-ttu-id="149d1-211">`UseWebApiParameterConventionsAttribute` 用于应用 `WebApiParameterConventionsApplicationModelConvention` 操作约定。</span><span class="sxs-lookup"><span data-stu-id="149d1-211">The `UseWebApiParameterConventionsAttribute` is used to apply the `WebApiParameterConventionsApplicationModelConvention` action convention.</span></span> <span data-ttu-id="149d1-212">此约定指定用作操作参数的简单类型默认来自 URI，而复杂类型来自请求正文。</span><span class="sxs-lookup"><span data-stu-id="149d1-212">This convention specifies that simple types used as action parameters are bound from the URI by default, while complex types are bound from the request body.</span></span>

### <a name="routes"></a><span data-ttu-id="149d1-213">路由</span><span class="sxs-lookup"><span data-stu-id="149d1-213">Routes</span></span>

<span data-ttu-id="149d1-214">`UseWebApiRoutesAttribute` 控制是否应用 `WebApiApplicationModelConvention` 控制器约定。</span><span class="sxs-lookup"><span data-stu-id="149d1-214">The `UseWebApiRoutesAttribute` controls whether the `WebApiApplicationModelConvention` controller convention is applied.</span></span> <span data-ttu-id="149d1-215">启用后，此约定用于向路由添加对[区域](xref:mvc/controllers/areas)的支持。</span><span class="sxs-lookup"><span data-stu-id="149d1-215">When enabled, this convention is used to add support for [areas](xref:mvc/controllers/areas) to the route.</span></span>

<span data-ttu-id="149d1-216">除了一组约定外，该兼容性包还包含一个 `System.Web.Http.ApiController` 基类，用于替换 Web API 提供的等效项。</span><span class="sxs-lookup"><span data-stu-id="149d1-216">In addition to a set of conventions, the compatibility package includes a `System.Web.Http.ApiController` base class that replaces the one provided by Web API.</span></span> <span data-ttu-id="149d1-217">这允许针对 Web API 编写并且继承自 `ApiController` 的控制器在 ASP.NET Core MVC 上运行时，能够按照设计的方式运行。</span><span class="sxs-lookup"><span data-stu-id="149d1-217">This allows your controllers written for Web API and inheriting from its `ApiController` to work as they were designed, while running on ASP.NET Core MVC.</span></span> <span data-ttu-id="149d1-218">此控制器基类使用上面列出的所有 `UseWebApi*` 属性进行修饰。</span><span class="sxs-lookup"><span data-stu-id="149d1-218">This base controller class is decorated with all of the `UseWebApi*` attributes listed above.</span></span> <span data-ttu-id="149d1-219">`ApiController` 公开了与在 Web API 中找到的属性、方法和结果类型兼容的属性、方法和结果类型。</span><span class="sxs-lookup"><span data-stu-id="149d1-219">The `ApiController` exposes properties, methods, and result types that are compatible with those found in Web API.</span></span>

## <a name="using-apiexplorer-to-document-your-app"></a><span data-ttu-id="149d1-220">使用 ApiExplorer 记录应用</span><span class="sxs-lookup"><span data-stu-id="149d1-220">Using ApiExplorer to Document Your App</span></span>

<span data-ttu-id="149d1-221">应用程序模型在每个级别公开了 [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) 属性，该属性可用于遍历应用的结构。</span><span class="sxs-lookup"><span data-stu-id="149d1-221">The application model exposes an [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) property at each level that can be used to traverse the app's structure.</span></span> <span data-ttu-id="149d1-222">这可用于[使用 Swagger 等工具为 Web API 生成帮助页](xref:tutorials/web-api-help-pages-using-swagger)。</span><span class="sxs-lookup"><span data-stu-id="149d1-222">This can be used to [generate help pages for your Web APIs using tools like Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="149d1-223">`ApiExplorer` 属性公开了 `IsVisible` 属性，后者可设置为指定应公开的应用模型部分。</span><span class="sxs-lookup"><span data-stu-id="149d1-223">The `ApiExplorer` property exposes an `IsVisible` property that can be set to specify which parts of your app's model should be exposed.</span></span> <span data-ttu-id="149d1-224">可以使用约定配置此设置：</span><span class="sxs-lookup"><span data-stu-id="149d1-224">You can configure this setting using a convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

<span data-ttu-id="149d1-225">使用此方法（和附加约定，如有需要），可以在应用中的任何级别启用或禁用 API 可见性。</span><span class="sxs-lookup"><span data-stu-id="149d1-225">Using this approach (and additional conventions if required), you can enable or disable API visibility at any level within your app.</span></span> 
