---
title: ASP.NET Core Blazor 布局
author: guardrex
description: 了解如何为 Blazor 应用创建可重用的布局组件。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/layouts
ms.openlocfilehash: 05a38c10e18407d50422192ab1ddf3ff4b0f3a5b
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800371"
---
# <a name="aspnet-core-blazor-layouts"></a>ASP.NET Core Blazor 布局

作者： [Rainer Stropek](https://www.timecockpit.com)和[Luke Latham](https://github.com/guardrex)

某些应用元素（例如菜单、版权消息和公司徽标）通常是应用整体布局的一部分，并由应用中的每个组件使用。 每次元素需要更新时，将这些元素的代码复制到应用的所有组件&mdash;并不是一种有效的方法，必须更新每个组件。 此类复制很难维护，并且可能会在一段时间后导致内容不一致。 *布局*解决了此问题。

从技术上讲，布局只是另一个组件。 布局在 Razor 模板或代码中C#定义，可以使用[数据绑定](xref:blazor/components#data-binding)、[依赖关系注入](xref:blazor/dependency-injection)和其他组件方案。

若要将*组件*转换为*布局*，请执行以下操作：

* 继承自`LayoutComponentBase`，后者`Body`定义布局内呈现的内容的属性。
* 使用 Razor 语法`@Body`指定布局标记中呈现内容的位置。

下面的代码示例显示了布局组件*MainLayout*的 Razor 模板。 布局在导航`LayoutComponentBase`栏和页脚`@Body`之间继承和设置：

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

在基于一个 Blazor 应用程序模板的应用程序中， `MainLayout`组件（*MainLayout*）位于应用程序的*共享*文件夹中。

## <a name="default-layout"></a>默认布局

在应用程序的*app.config*文件中`Router`的组件中指定默认的应用布局。 以下`Router`由默认 Blazor 模板提供的组件将默认布局设置`MainLayout`为组件：

[!code-cshtml[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

若要为`NotFound`内容提供默认布局，请`LayoutView`指定`NotFound`内容的：

[!code-cshtml[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

有关`Router`组件的详细信息，请参阅<xref:blazor/routing>。

## <a name="specify-a-layout-in-a-component"></a>在组件中指定布局

使用 Razor 指令`@layout`将布局应用于组件。 编译器将转换`@layout` `LayoutAttribute`为，它将应用于组件类。

以下`MasterList`组件的内容将插入`MasterLayout`到中的位置`@Body`：

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a>集中式布局选择

应用的每个文件夹都可以选择包含名为 *_Imports*的模板文件。 编译器将导入文件中指定的指令包含在同一文件夹中的所有 Razor 模板中，并以递归方式包含在其所有子文件夹中。 因此， *_Imports*文件包含`@layout MyCoolLayout`可确保文件夹中的所有组件都使用。 `MyCoolLayout` 无需重复添加`@layout MyCoolLayout`到文件夹和子文件夹中的所有*razor*文件。 `@using`指令也以相同的方式应用于组件。

以下 *_Imports*文件导入：

* `MyCoolLayout`。
* 同一文件夹和所有子文件夹中的所有 Razor 组件。
* `BlazorApp1.Data` 命名空间。
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

*_Imports*文件类似于[razor 视图和页面的 _ViewImports 文件](xref:mvc/views/layout#importing-shared-directives)，但专门应用于 razor 组件文件。

## <a name="nested-layouts"></a>嵌套布局

应用可由嵌套布局组成。 组件可以引用另一个布局。 例如，使用嵌套布局创建多层菜单结构。

下面的示例演示如何使用嵌套的布局。 *EpisodesComponent*文件是要显示的组件。 组件引用`MasterListLayout`：

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

*MasterListLayout*文件提供`MasterListLayout`。 布局引用在呈现它时`MasterLayout`的另一个布局。 `EpisodesComponent`在出现以下`@Body`情况时呈现：

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

最后， `MasterLayout`在*MasterLayout*中包含顶级布局元素，如标头、主菜单和脚注。 `MasterListLayout`在出现的情况下`@Body`呈现： `EpisodesComponent`

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a>其他资源

* <xref:mvc/views/layout>
