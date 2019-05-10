---
title: ASP.NET Core 中的应用程序部件
author: ardalis
description: 了解如何使用应用程序部件（应用资源的抽象）来发现或避免从程序集加载功能。
ms.author: riande
ms.date: 01/04/2017
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 5d5e79d3afe0eee6d0b126d4642ccd5f61bff409
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64888132"
---
# <a name="application-parts-in-aspnet-core"></a><span data-ttu-id="90226-103">ASP.NET Core 中的应用程序部件</span><span class="sxs-lookup"><span data-stu-id="90226-103">Application Parts in ASP.NET Core</span></span>

<span data-ttu-id="90226-104">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="90226-104">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="90226-105">*应用程序部件*是应用程序资源的一种抽象，可通过它发现控制器、视图组件或标记帮助程序等 MVC 功能。</span><span class="sxs-lookup"><span data-stu-id="90226-105">An *Application Part* is an abstraction over the resources of an application, from which MVC features like controllers, view components, or tag helpers may be discovered.</span></span> <span data-ttu-id="90226-106">AssemblyPart 就是一种应用程序部件，用于封装程序集引用以及公开类型和编译引用。</span><span class="sxs-lookup"><span data-stu-id="90226-106">One example of an application part is an AssemblyPart, which encapsulates an assembly reference and exposes types and compilation references.</span></span> <span data-ttu-id="90226-107">*功能提供程序*使用应用程序部件填充 ASP.NET Core MVC 应用的功能。</span><span class="sxs-lookup"><span data-stu-id="90226-107">*Feature providers* work with application parts to populate the features of an ASP.NET Core MVC app.</span></span> <span data-ttu-id="90226-108">应用程序部件的主要用例是允许将应用配置为从程序集中发现（或避免加载）MVC 功能。</span><span class="sxs-lookup"><span data-stu-id="90226-108">The main use case for application parts is to allow you to configure your app to discover (or avoid loading) MVC features from an assembly.</span></span>

## <a name="introducing-application-parts"></a><span data-ttu-id="90226-109">应用程序部件简介</span><span class="sxs-lookup"><span data-stu-id="90226-109">Introducing Application Parts</span></span>

<span data-ttu-id="90226-110">MVC 应用从[应用程序部件](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart)中加载其功能。</span><span class="sxs-lookup"><span data-stu-id="90226-110">MVC apps load their features from [application parts](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span></span> <span data-ttu-id="90226-111">具体而言，[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) 类表示受程序集支持的应用程序部件。</span><span class="sxs-lookup"><span data-stu-id="90226-111">In particular, the [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) class represents an application part that's backed by an assembly.</span></span> <span data-ttu-id="90226-112">可以使用这些类发现和加载 MVC 功能，比如控制器、视图组件、标记帮助程序和 Razor 编译源。</span><span class="sxs-lookup"><span data-stu-id="90226-112">You can use these classes to discover and load MVC features, such as controllers, view components, tag helpers, and razor compilation sources.</span></span> <span data-ttu-id="90226-113">[ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) 负责跟踪可用于 MVC 应用的应用程序部件和功能提供程序。</span><span class="sxs-lookup"><span data-stu-id="90226-113">The [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) is responsible for tracking the application parts and feature providers available to the MVC app.</span></span> <span data-ttu-id="90226-114">配置 MVC 时，可以与 `Startup` 中的 `ApplicationPartManager` 交互：</span><span class="sxs-lookup"><span data-stu-id="90226-114">You can interact with the `ApplicationPartManager` in `Startup` when you configure MVC:</span></span>

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services.AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services.AddMvc()
    .ConfigureApplicationPartManager(apm => apm.ApplicationParts.Add(part));
```

<span data-ttu-id="90226-115">默认情况下，MVC 将搜索依赖项树并查找控制器（甚至在其他程序集中）。</span><span class="sxs-lookup"><span data-stu-id="90226-115">By default MVC will search the dependency tree and find controllers (even in other assemblies).</span></span> <span data-ttu-id="90226-116">若要加载任意程序集（例如，从在编译时未引用的插件），可以使用应用程序部件。</span><span class="sxs-lookup"><span data-stu-id="90226-116">To load an arbitrary assembly (for instance, from a plugin that isn't referenced at compile time), you can use an application part.</span></span>

<span data-ttu-id="90226-117">可以使用应用程序部件*避免*查找特定程序集或位置中的控制器。</span><span class="sxs-lookup"><span data-stu-id="90226-117">You can use application parts to *avoid* looking for controllers in a particular assembly or location.</span></span> <span data-ttu-id="90226-118">可以通过修改 `ApplicationPartManager` 的 `ApplicationParts` 集合，控制应用可用的部件（或程序集）。</span><span class="sxs-lookup"><span data-stu-id="90226-118">You can control which parts (or assemblies) are available to the app by modifying the `ApplicationParts` collection of the `ApplicationPartManager`.</span></span> <span data-ttu-id="90226-119">`ApplicationParts` 集合中条目的顺序并不重要。</span><span class="sxs-lookup"><span data-stu-id="90226-119">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="90226-120">重要的是在使用 `ApplicationPartManager` 配置容器中的服务之前，对该类进行完全配置。</span><span class="sxs-lookup"><span data-stu-id="90226-120">It's important to fully configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="90226-121">例如，应在调用 `AddControllersAsServices` 之前完全配置 `ApplicationPartManager`。</span><span class="sxs-lookup"><span data-stu-id="90226-121">For example, you should fully configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="90226-122">如果未进行完全配置，就意味着，在该方法调用之后添加的应用程序部件中的控制器不受影响（不注册为服务），这可能导致不正确的应用程序行为。</span><span class="sxs-lookup"><span data-stu-id="90226-122">Failing to do so, will mean that controllers in application parts added after that method call won't be affected (won't get registered as services) which might result in incorrect behavior of your application.</span></span>

<span data-ttu-id="90226-123">如果某个程序集包含你不想使用的控制器，则将该程序集从 `ApplicationPartManager` 中删除：</span><span class="sxs-lookup"><span data-stu-id="90226-123">If you have an assembly that contains controllers you don't want to be used, remove it from the `ApplicationPartManager`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm =>
    {
        var dependentLibrary = apm.ApplicationParts
            .FirstOrDefault(part => part.Name == "DependentLibrary");

        if (dependentLibrary != null)
        {
           apm.ApplicationParts.Remove(dependentLibrary);
        }
    })
```

<span data-ttu-id="90226-124">除了项目的程序集及其从属程序集，`ApplicationPartManager` 还默认包含 `Microsoft.AspNetCore.Mvc.TagHelpers` 和 `Microsoft.AspNetCore.Mvc.Razor` 的部件。</span><span class="sxs-lookup"><span data-stu-id="90226-124">In addition to your project's assembly and its dependent assemblies, the `ApplicationPartManager` will include parts for `Microsoft.AspNetCore.Mvc.TagHelpers` and `Microsoft.AspNetCore.Mvc.Razor` by default.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="90226-125">应用程序功能提供程序</span><span class="sxs-lookup"><span data-stu-id="90226-125">Application Feature Providers</span></span>

<span data-ttu-id="90226-126">应用程序功能提供程序用于检查应用程序部件，并为这些部件提供功能。</span><span class="sxs-lookup"><span data-stu-id="90226-126">Application Feature Providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="90226-127">以下 MVC 功能有内置功能提供程序：</span><span class="sxs-lookup"><span data-stu-id="90226-127">There are built-in feature providers for the following MVC features:</span></span>

* [<span data-ttu-id="90226-128">控制器</span><span class="sxs-lookup"><span data-stu-id="90226-128">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="90226-129">元数据引用</span><span class="sxs-lookup"><span data-stu-id="90226-129">Metadata Reference</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [<span data-ttu-id="90226-130">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="90226-130">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="90226-131">视图组件</span><span class="sxs-lookup"><span data-stu-id="90226-131">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="90226-132">功能提供程序从 `IApplicationFeatureProvider<T>` 继承，其中 `T` 是功能的类型。</span><span class="sxs-lookup"><span data-stu-id="90226-132">Feature providers inherit from `IApplicationFeatureProvider<T>`, where `T` is the type of the feature.</span></span> <span data-ttu-id="90226-133">你可以为上面列出的任意 MVC 功能类型实现自己的功能提供程序。</span><span class="sxs-lookup"><span data-stu-id="90226-133">You can implement your own feature providers for any of MVC's feature types listed above.</span></span> <span data-ttu-id="90226-134">`ApplicationPartManager.FeatureProviders` 集合中功能提供程序的顺序可能很重要，因为靠后的提供程序可以对前面的提供程序所执行的操作作出反应。</span><span class="sxs-lookup"><span data-stu-id="90226-134">The order of feature providers in the `ApplicationPartManager.FeatureProviders` collection can be important, since later providers can react to actions taken by previous providers.</span></span>

### <a name="sample-generic-controller-feature"></a><span data-ttu-id="90226-135">示例:通用控制器功能</span><span class="sxs-lookup"><span data-stu-id="90226-135">Sample: Generic controller feature</span></span>

<span data-ttu-id="90226-136">默认情况下，ASP.NET Core MVC 会忽略泛型控制器（例如，`SomeController<T>`）。</span><span class="sxs-lookup"><span data-stu-id="90226-136">By default, ASP.NET Core MVC ignores generic controllers (for example, `SomeController<T>`).</span></span> <span data-ttu-id="90226-137">此示例使用的控制器功能提供程序在默认提供程序后面运行并为指定的类型列表（在 `EntityTypes.Types` 中定义）添加泛型控制器实例：</span><span class="sxs-lookup"><span data-stu-id="90226-137">This sample uses a controller feature provider that runs after the default provider and adds generic controller instances for a specified list of types (defined in `EntityTypes.Types`):</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

<span data-ttu-id="90226-138">实体类型：</span><span class="sxs-lookup"><span data-stu-id="90226-138">The entity types:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

<span data-ttu-id="90226-139">将该功能提供程序添加到 `Startup` 中：</span><span class="sxs-lookup"><span data-stu-id="90226-139">The feature provider is added in `Startup`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm => 
        apm.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

<span data-ttu-id="90226-140">默认情况下，用于路由的泛型控制器名称的格式为 *GenericController\`1[Widget]*，而不是 *Widget*。</span><span class="sxs-lookup"><span data-stu-id="90226-140">By default, the generic controller names used for routing would be of the form *GenericController\`1[Widget]* instead of *Widget*.</span></span> <span data-ttu-id="90226-141">以下属性用于修改该名称，以便与控制器使用的泛型类型对应：</span><span class="sxs-lookup"><span data-stu-id="90226-141">The following attribute is used to modify the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="90226-142">`GenericController` 类：</span><span class="sxs-lookup"><span data-stu-id="90226-142">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

<span data-ttu-id="90226-143">当请求匹配的路由时，结果如下：</span><span class="sxs-lookup"><span data-stu-id="90226-143">The result, when a matching route is requested:</span></span>

![示例应用的示例输出为“Hello from a generic Sprocket controller.”](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a><span data-ttu-id="90226-145">示例:显示可用功能</span><span class="sxs-lookup"><span data-stu-id="90226-145">Sample: Display available features</span></span>

<span data-ttu-id="90226-146">可循环访问可用于应用的已填充功能，方法为通过[依赖关系注入](../../fundamentals/dependency-injection.md)请求 `ApplicationPartManager`，并用它来填充相应功能的实例：</span><span class="sxs-lookup"><span data-stu-id="90226-146">You can iterate through the populated features available to your app by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md) and using it to populate instances of the appropriate features:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="90226-147">示例输出：</span><span class="sxs-lookup"><span data-stu-id="90226-147">Example output:</span></span>

![示例应用的示例输出](app-parts/_static/available-features.png)
