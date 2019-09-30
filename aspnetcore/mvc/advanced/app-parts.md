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
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a>使用 ASP.NET Core 中的应用程序部件共享控制器、视图、Razor Pages 等

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts)（[如何下载](xref:index#how-to-download-a-sample)）

应用程序部件是对应用资源的抽象化。  借助应用程序部件，ASP.NET Core 可以发现控制器、视图组件、标记帮助器、Razor Pages、Razor 编译源等。 [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) 是一种应用程序部件。 `AssemblyPart` 用于封装程序集引用，并公开类型和编译引用。

功能提供程序使用应用程序部件填充 ASP.NET Core 应用的功能。  应用程序部件的主要用例是将应用配置为从程序集中发现（或避免加载）ASP.NET Core 功能。 例如，可能需要在多个应用之间共享通用功能。 借助应用程序部件，你可以与多个应用共享包含控制器、视图、Razor Pages、Razor 编译源、标记帮助器等的程序集 (DLL)。 相对于在多个项目中复制代码，首选共享程序集。

ASP.NET Core 应用从 <xref:System.Web.WebPages.ApplicationPart> 加载功能。 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 类表示受程序集支持的应用程序部件。

## <a name="load-aspnet-core-features"></a>加载 ASP.NET Core 功能

使用 `ApplicationPart` 和 `AssemblyPart` 类发现并加载 ASP.NET Core 功能（控制器、视图组件等）。 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> 跟踪可用的应用程序部件和功能提供程序。 在 `Startup.ConfigureServices` 中配置 `ApplicationPartManager`：

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

以下代码提供使用 `AssemblyPart` 配置 `ApplicationPartManager` 的可选方法：

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

前面的两个代码示例从程序集加载 `SharedController`。 `SharedController` 未在应用程序的项目中。 请参阅 [WebAppParts 解决方案](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts)示例下载。

### <a name="include-views"></a>包含视图

包含程序集中的视图：

* 在共享项目文件中添加以下标记：

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* 将 <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> 添加到 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>：

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a>阻止加载资源

可以使用应用程序部件来避免加载特定程序集或位置中的资源。  添加或删除 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 集合的成员，将隐藏或提供资源。 `ApplicationParts` 集合中条目的顺序并不重要。 在使用 `ApplicationPartManager` 配置容器中的服务之前，对该类进行配置。 例如，在调用 `AddControllersAsServices` 之前配置 `ApplicationPartManager`。 在 `ApplicationParts` 集合上调用 `Remove`，将删除资源。

以下代码使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 删除应用中的 `MyDependentLibrary`：[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

`ApplicationPartManager` 包括以下内容的部件：

* 应用的程序集和依赖程序集。
* `Microsoft.AspNetCore.Mvc.TagHelpers`。
* `Microsoft.AspNetCore.Mvc.Razor`。

## <a name="application-feature-providers"></a>应用程序功能提供程序

应用程序功能提供程序用于检查应用程序部件，并为这些部件提供功能。 以下 ASP.NET Core 功能有内置功能提供程序：

* [Controllers](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [标记帮助程序](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [视图组件](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

功能提供程序从 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> 继承，其中 `T` 是功能的类型。 可以为上面列出的任意功能类型实现功能提供程序。 `ApplicationPartManager.FeatureProviders` 中的功能提供程序的顺序可能影响运行时行为。 较晚添加的提供程序可能会影响较早添加的提供程序执行的操作。

### <a name="generic-controller-feature"></a>通用控制器功能

ASP.NET Core 将忽略[泛型控制器](/dotnet/csharp/programming-guide/generics/generic-classes)。 泛型控制器包含类型参数（如 `MyController<T>`）。 以下示例添加一组指定类型的泛型控制器实例：

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

类型在 `EntityTypes.Types` 中定义：

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

将该功能提供程序添加到 `Startup` 中：

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

用于路由的泛型控制器名称的格式为 GenericController`1[Widget]  ，而不是 Widget  。 以下属性将修改该名称，以便与控制器使用的泛型类型对应：

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

`GenericController` 类：

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

例如，请求 `https://localhost:5001/Sprocket` 的 URL 将引发以下响应：

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a>显示可用功能

通过[依存关系注入](../../fundamentals/dependency-injection.md)请求 `ApplicationPartManager` 即可以枚举应用的可用功能：

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[下载示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)使用前面的代码显示应用功能：

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
