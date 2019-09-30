---
title: ASP.NET Core 中的应用程序部件
author: rick-anderson
description: 使用 ASP.NET Core 中的应用程序部件共享控制器、视图、Razor Pages 等
ms.author: riande
ms.date: 05/14/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 4b4c8c554a7045a180b56cf9998ab1a8496cde1b
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207347"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a><span data-ttu-id="f1c4c-103">使用 ASP.NET Core 中的应用程序部件共享控制器、视图、Razor Pages 等</span><span class="sxs-lookup"><span data-stu-id="f1c4c-103">Share controllers, views, Razor Pages and more with Application Parts in ASP.NET Core</span></span>

<span data-ttu-id="f1c4c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f1c4c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f1c4c-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f1c4c-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f1c4c-106">应用程序部件是对应用资源的抽象化。 </span><span class="sxs-lookup"><span data-stu-id="f1c4c-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="f1c4c-107">借助应用程序部件，ASP.NET Core 可以发现控制器、视图组件、标记帮助器、Razor Pages、Razor 编译源等。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="f1c4c-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) 是一种应用程序部件。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="f1c4c-109">`AssemblyPart` 用于封装程序集引用，并公开类型和编译引用。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="f1c4c-110">功能提供程序使用应用程序部件填充 ASP.NET Core 应用的功能。 </span><span class="sxs-lookup"><span data-stu-id="f1c4c-110">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="f1c4c-111">应用程序部件的主要用例是将应用配置为从程序集中发现（或避免加载）ASP.NET Core 功能。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="f1c4c-112">例如，可能需要在多个应用之间共享通用功能。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="f1c4c-113">借助应用程序部件，你可以与多个应用共享包含控制器、视图、Razor Pages、Razor 编译源、标记帮助器等的程序集 (DLL)。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="f1c4c-114">相对于在多个项目中复制代码，首选共享程序集。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="f1c4c-115">ASP.NET Core 应用从 <xref:System.Web.WebPages.ApplicationPart> 加载功能。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="f1c4c-116"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 类表示受程序集支持的应用程序部件。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="f1c4c-117">加载 ASP.NET Core 功能</span><span class="sxs-lookup"><span data-stu-id="f1c4c-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="f1c4c-118">使用 `ApplicationPart` 和 `AssemblyPart` 类发现并加载 ASP.NET Core 功能（控制器、视图组件等）。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-118">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="f1c4c-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> 跟踪可用的应用程序部件和功能提供程序。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="f1c4c-120">在 `Startup.ConfigureServices` 中配置 `ApplicationPartManager`：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="f1c4c-121">以下代码提供使用 `AssemblyPart` 配置 `ApplicationPartManager` 的可选方法：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="f1c4c-122">前面的两个代码示例从程序集加载 `SharedController`。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="f1c4c-123">`SharedController` 未在应用程序的项目中。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-123">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="f1c4c-124">请参阅 [WebAppParts 解决方案](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts)示例下载。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="f1c4c-125">包含视图</span><span class="sxs-lookup"><span data-stu-id="f1c4c-125">Include views</span></span>

<span data-ttu-id="f1c4c-126">包含程序集中的视图：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-126">To include views in the assembly:</span></span>

* <span data-ttu-id="f1c4c-127">在共享项目文件中添加以下标记：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-127">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="f1c4c-128">将 <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> 添加到 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-128">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="f1c4c-129">阻止加载资源</span><span class="sxs-lookup"><span data-stu-id="f1c4c-129">Prevent loading resources</span></span>

<span data-ttu-id="f1c4c-130">可以使用应用程序部件来避免加载特定程序集或位置中的资源。 </span><span class="sxs-lookup"><span data-stu-id="f1c4c-130">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="f1c4c-131">添加或删除 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 集合的成员，将隐藏或提供资源。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-131">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="f1c4c-132">`ApplicationParts` 集合中条目的顺序并不重要。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-132">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="f1c4c-133">在使用 `ApplicationPartManager` 配置容器中的服务之前，对该类进行配置。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-133">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="f1c4c-134">例如，在调用 `AddControllersAsServices` 之前配置 `ApplicationPartManager`。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-134">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="f1c4c-135">在 `ApplicationParts` 集合上调用 `Remove`，将删除资源。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-135">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="f1c4c-136">以下代码使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 删除应用中的 `MyDependentLibrary`：[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="f1c4c-136">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="f1c4c-137">`ApplicationPartManager` 包括以下内容的部件：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-137">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="f1c4c-138">应用的程序集和依赖程序集。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-138">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="f1c4c-139">`Microsoft.AspNetCore.Mvc.TagHelpers`。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="f1c4c-140">`Microsoft.AspNetCore.Mvc.Razor`。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-140">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="f1c4c-141">应用程序功能提供程序</span><span class="sxs-lookup"><span data-stu-id="f1c4c-141">Application feature providers</span></span>

<span data-ttu-id="f1c4c-142">应用程序功能提供程序用于检查应用程序部件，并为这些部件提供功能。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-142">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="f1c4c-143">以下 ASP.NET Core 功能有内置功能提供程序：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-143">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="f1c4c-144">Controllers</span><span class="sxs-lookup"><span data-stu-id="f1c4c-144">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="f1c4c-145">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="f1c4c-145">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="f1c4c-146">视图组件</span><span class="sxs-lookup"><span data-stu-id="f1c4c-146">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="f1c4c-147">功能提供程序从 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> 继承，其中 `T` 是功能的类型。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-147">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="f1c4c-148">可以为上面列出的任意功能类型实现功能提供程序。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-148">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="f1c4c-149">`ApplicationPartManager.FeatureProviders` 中的功能提供程序的顺序可能影响运行时行为。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-149">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="f1c4c-150">较晚添加的提供程序可能会影响较早添加的提供程序执行的操作。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-150">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="f1c4c-151">通用控制器功能</span><span class="sxs-lookup"><span data-stu-id="f1c4c-151">Generic controller feature</span></span>

<span data-ttu-id="f1c4c-152">ASP.NET Core 将忽略[泛型控制器](/dotnet/csharp/programming-guide/generics/generic-classes)。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-152">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="f1c4c-153">泛型控制器包含类型参数（如 `MyController<T>`）。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-153">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="f1c4c-154">以下示例添加一组指定类型的泛型控制器实例：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-154">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="f1c4c-155">类型在 `EntityTypes.Types` 中定义：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-155">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="f1c4c-156">将该功能提供程序添加到 `Startup` 中：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-156">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="f1c4c-157">用于路由的泛型控制器名称的格式为 GenericController\`1[Widget]  ，而不是 Widget  。</span><span class="sxs-lookup"><span data-stu-id="f1c4c-157">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="f1c4c-158">以下属性将修改该名称，以便与控制器使用的泛型类型对应：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-158">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="f1c4c-159">`GenericController` 类：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-159">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="f1c4c-160">例如，请求 `https://localhost:5001/Sprocket` 的 URL 将引发以下响应：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-160">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="f1c4c-161">显示可用功能</span><span class="sxs-lookup"><span data-stu-id="f1c4c-161">Display available features</span></span>

<span data-ttu-id="f1c4c-162">通过[依存关系注入](../../fundamentals/dependency-injection.md)请求 `ApplicationPartManager` 即可以枚举应用的可用功能：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-162">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="f1c4c-163">[下载示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)使用前面的代码显示应用功能：</span><span class="sxs-lookup"><span data-stu-id="f1c4c-163">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```
