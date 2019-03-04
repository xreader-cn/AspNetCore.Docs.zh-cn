---
title: ASP.NET Core 中的区域
author: rick-anderson
description: 了解 ASP.NET MVC 的区域功能如何将相关功能以单独的名称空间（用于路由）和文件夹结构（用于视图）的形式组织到一个组中。
ms.author: riande
ms.date: 02/14/2019
uid: mvc/controllers/areas
ms.openlocfilehash: c21eed04ea68512515da262b6b6895dc1a821039
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833522"
---
# <a name="areas-in-aspnet-core"></a>ASP.NET Core 中的区域

作者：[Dhananjay Kumar](https://twitter.com/debug_mode) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

区域是 ASP.NET 功能，用于将相关功能以单独的名称空间（用于路由）和文件夹结构（用于视图）的形式组织到一个组中。 使用区域通过为 `controller` 和 `action` 或 Razor Page `page` 添加其他路由参数 `area`，创建用于路由目的的层次结构。

区域提供了一种将 ASP.NET Core Web 应用划分为更小的功能组的方法，每个功能组都有自己的一组 Razor Pages、控制器、视图和模型。 区域实际上是应用内的结构。 在 ASP.NET Core Web 项目中，Pages、模型、控制器和视图等逻辑组件保存在不同的文件夹中。 ASP.NET Core 运行时使用命名约定来创建这些组件之间的关系。 对于大型应用，将应用分区为独立的高级功能区域可能更有利。 例如，具有多个业务单位（如结账、计费、搜索等）的电子商务应用。 每个单位都有自己的区域，以包含视图、控制器、Razor Pages 和模型。

如果发生以下情况，请考虑在项目中使用区域：

* 应用由可以进行逻辑分隔的多个高级功能组件组成。
* 想对应用进行分区，以便可以独立处理每个功能区域。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)（[如何下载](xref:index#how-to-download-a-sample)）。 下载示例提供了用于测试区域的基本应用。

## <a name="areas-for-controllers-with-views"></a>带视图的控制器区域

使用区域、控制器和视图的典型 ASP.NET Core Web 应用包含以下内容：

* [区域文件夹结构](#area-folder-structure)。
* 使用[&lbrack;区域&rbrack;](#attribute)属性（该属性将控制器与区域关联）进行修饰的控制器：[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]
* [添加到启动的区域路由](#add-area-route)：[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

## <a name="area-folder-structure"></a>区域文件夹结构
请考虑具有两个逻辑组（产品和服务）的应用。 使用区域，文件夹结构类似于以下内容：

* 项目名称
  * 区域
    * 产品
      * Controllers
        * HomeController.cs
        * ManageController.cs
      * 视图
        * 主页
          * Index.cshtml
        * 管理
          * Index.cshtml
          * About.cshtml
    * 服务
      * Controllers
        * HomeController.cs
      * 视图
        * 主页
          * Index.cshtml

虽然前面的布局是使用区域时的典型布局，但只需要视图文件即可使用此文件夹结构。 视图发现按以下顺序搜索匹配的区域视图文件：

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

控制器和模型等非视图文件夹的位置无关紧要。 例如，控制器和模型文件夹不是必需的。 控制器和模型的内容是编译成 .dll 文件的代码。 在对该视图发出请求之前，不会编译视图的内容。

<!-- TODO review:
The content of the *Views* isn't compiled until a request to that view has been made.

What about precompiled views? 
 -->
<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>将控制器与区域关联

使用[&lbrack;区域&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性指定区域控制器：

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>添加区域路由

区域路由通常使用传统路由，而不使用属性路由。 传统路由依赖于顺序。 一般情况下，具有区域的路由应放在路由表中靠前的位置，因为它们比没有区域的路由更特定。

如果所有区域的 url 空间一致，则 `{area:...}` 可用作路由模板中的令牌：

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

在前面的代码中，`exists` 应用了路由必须与区域匹配的约束。 使用 `{area:...}` 是将路由添加到区域的最简单的机制。

以下代码使用 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> 创建两个命名区域路由：

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

将 `MapAreaRoute` 与 ASP.NET Core 2.2 配合使用时，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/7772)。

有关详细信息，请参阅[区域路由](xref:mvc/controllers/routing#areas)。

### <a name="link-generation-with-areas"></a>区域的链接生成

[示例下载](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)中的以下代码显示指定区域的链接生成：

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

使用上述代码生成的链接在应用的任何位置都有效。

示例下载包含[部分视图](xref:mvc/views/partial)，该视图包含以前的链接和相同的链接（未指定区域）。 在[布局文件]()中引用部分视图，因此应用中的每个页面都显示生成的链接。 在未指定区域的情况下生成的链接仅在从同一区域和控制器中的页面引用时才有效。

如果未指定区域或控制器，路由取决于环境值。 当前请求的当前路由值被视为链接生成的环境值。 在许多情况下，对于示例应用，使用环境值会生成错误的链接。

有关详细信息，请参阅[路由到控制器操作](xref:mvc/controllers/routing)。

### <a name="shared-layout-for-areas-using-the-viewstartcshtml-file"></a>使用 _ViewStart.cshtml 文件的共享区域布局

要共享整个应用的常用布局，请将 _ViewStart.cshtml 移动到应用程序根文件夹。

<!-- This section will be completed after https://github.com/aspnet/Docs/pull/10978 is merged.
<a name="arp"></a>

## Areas for Razor Pages
-->
<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>更改存储视图的默认区域文件夹

以下代码将默认的区域文件夹从 `"Areas"` 改为`"MyAreas"`：

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<!-- TODO review - can we delete this. Areas doesn't change publishing - right? -->
### <a name="publishing-areas"></a>发布区域

当 .csproj 文件中包含 `<Project Sdk="Microsoft.NET.Sdk.Web">` 时，所有 `*.cshtml` 和 `wwwroot/**` 文件将发布到输出。
