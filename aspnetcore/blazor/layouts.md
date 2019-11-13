---
title: Blazor 布局 ASP.NET Core
author: guardrex
description: 了解如何为 Blazor 应用创建可重用的布局组件。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
no-loc:
- Blazor
uid: blazor/layouts
ms.openlocfilehash: 3546259fc6b622a6137a6baa8f446c5f43af1cab
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962808"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a>Blazor 布局 ASP.NET Core

作者： [Rainer Stropek](https://www.timecockpit.com)和[Luke Latham](https://github.com/guardrex)

某些应用元素（例如菜单、版权消息和公司徽标）通常是应用整体布局的一部分，并由应用中的每个组件使用。 将这些元素的代码复制到应用程序的所有组件中不是一种有效的方法&mdash;每次元素需要更新时，必须更新每个组件。 此类复制很难维护，并且可能会在一段时间后导致内容不一致。 *布局*解决了此问题。

从技术上讲，布局只是另一个组件。 布局在 Razor 模板或代码中C#定义，可以使用[数据绑定](xref:blazor/components#data-binding)、[依赖关系注入](xref:blazor/dependency-injection)和其他组件方案。

若要将*组件*转换为*布局*，请执行以下操作：

* 继承自 `LayoutComponentBase`，该属性定义布局内呈现内容的 `Body` 属性。
* 使用 Razor 语法 `@Body` 来指定布局标记中呈现内容的位置。

下面的代码示例显示了布局组件*MainLayout*的 Razor 模板。 布局会继承 `LayoutComponentBase`，并在导航栏和页脚之间设置 `@Body`：

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

在基于一个 Blazor 应用程序模板的应用程序中，`MainLayout` 组件（*MainLayout*）位于应用程序的*共享*文件夹中。

## <a name="default-layout"></a>默认布局

在应用的*app.config*文件中的 `Router` 组件中指定默认的应用布局。 以下 `Router` 组件由默认 Blazor 模板提供，用于将默认布局设置为 `MainLayout` 组件：

[!code-cshtml[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

若要为 `NotFound` 内容提供默认布局，请为 `NotFound` 内容指定 `LayoutView`：

[!code-cshtml[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

有关 `Router` 组件的详细信息，请参阅 <xref:blazor/routing>。

在路由器中将布局指定为默认布局是一项有用的做法，因为它可以基于每个组件或每个文件夹进行重写。 首选使用路由器设置应用的默认布局，因为这是最常见的方法。

## <a name="specify-a-layout-in-a-component"></a>在组件中指定布局

使用 Razor 指令 `@layout` 将布局应用于组件。 编译器将 `@layout` 转换为 `LayoutAttribute`，它将应用于组件类。

以下 `MasterList` 组件的内容会插入到 `MasterLayout` 中 `@Body`的位置：

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

直接在组件中指定布局会重写从 *_Imports*导入的路由器或 `@layout` 指令中设置的*默认布局*。

## <a name="centralized-layout-selection"></a>集中式布局选择

应用的每个文件夹可以选择性地包含名为 *_Imports*的模板文件。 编译器将导入文件中指定的指令包含在同一文件夹中的所有 Razor 模板中，并以递归方式包含在其所有子文件夹中。 因此，包含 `@layout MyCoolLayout` 的 *_Imports razor*文件可确保文件夹中的所有组件都使用 `MyCoolLayout`。 无需将 `@layout MyCoolLayout` 重复添加到文件夹和子文件夹中的所有*razor*文件中。 `@using` 指令也以相同的方式应用于组件。

以下 *_Imports*文件导入：

* `MyCoolLayout`
* 同一文件夹和所有子文件夹中的所有 Razor 组件。
* `BlazorApp1.Data` 命名空间。
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

*_Imports razor*文件类似于[razor 视图和页面的 _ViewImports cshtml 文件](xref:mvc/views/layout#importing-shared-directives)，但专门应用于 razor 组件文件。

在 *_Imports*中指定布局会重写指定为路由器*默认布局*的布局。

## <a name="nested-layouts"></a>嵌套布局

应用可由嵌套布局组成。 组件可以引用另一个布局。 例如，使用嵌套布局创建多层菜单结构。

下面的示例演示如何使用嵌套的布局。 *EpisodesComponent*文件是要显示的组件。 组件引用 `MasterListLayout`：

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

*MasterListLayout*文件提供 `MasterListLayout`。 布局引用在呈现时 `MasterLayout`的另一个布局。 `EpisodesComponent` 呈现 `@Body` 出现的位置：

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

最后， *MasterLayout*中的 `MasterLayout` 包含顶级布局元素，如标头、主菜单和脚注。 在显示 `@Body` 的情况下，将呈现与 `EpisodesComponent` `MasterListLayout`：

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a>其他资源

* <xref:mvc/views/layout>
