---
title: Razor 组件布局
author: guardrex
description: 了解如何创建可重用布局 Razor 组件应用程序的组件。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/layouts
ms.openlocfilehash: 31ed940ce40e3ae6e3744418cf241d396308f4fe
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515412"
---
# <a name="razor-components-layouts"></a>Razor 组件布局

通过[Rainer Stropek](https://www.timecockpit.com)

应用程序通常包含多个组件。 布局元素，如菜单、 版权消息和徽标，必须在所有组件上存在。 将这些布局元素的代码复制到的所有应用程序组件并不是有效的途径。 此类重复难以维护，并随着时间的推移可能导致不一致的内容。 *布局*解决此问题。

从技术上讲，布局是只是另一个组件。 布局定义 Razor 模板中或在C#代码，并可以包含[数据绑定](xref:razor-components/components#data-binding)，[依赖关系注入](xref:razor-components/dependency-injection)，和其他普通功能的组件。

打开两个其他方面*组件*到*布局*

* 布局组件必须继承自`LayoutComponentBase`。 `LayoutComponentBase` 定义`Body`属性，其中包含布局内呈现的内容。
* 布局组件使用`Body`属性指定的正文内容应呈现使用 Razor 语法`@Body`。 在呈现期间`@Body`布局的内容替换为。

下面的代码示例显示了布局组件的 Razor 模板。 请注意，使用`LayoutComponentBase`和`@Body`:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.cshtml)]

## <a name="use-a-layout-in-a-component"></a>在组件中使用的布局

使用 Razor 指令`@layout`要应用于组件的布局。 编译器将转换到此指令`LayoutAttribute`，其应用到 component 类。

下面的代码示例演示了这一概念。 此组件的内容插入到*MasterLayout*的位置处`@Body`:

```cshtml
@layout MasterLayout
@page "/master-list"

<h2>Master Episode List</h2>
```

## <a name="centralized-layout-selection"></a>选择集中式的布局

每个文件夹的应用程序可以选择包含名为的模板文件 *_ViewImports.cshtml*。 编译器包含在同一文件夹中的 Razor 模板和以递归方式在所有子文件夹中的所有视图导入文件中指定的指令。 因此， *_ViewImports.cshtml*文件中含有`@layout MainLayout`确保所有文件夹，请使用中的组件*MainLayout*布局。 无需反复添加`@layout`的所有 *.razor*文件。

请注意，使用默认模板 *_ViewImports.cshtml*机制，用于选择布局。 新创建的应用程序包含 *_ViewImports.cshtml*中的文件*组件/页*文件夹。

## <a name="nested-layouts"></a>嵌套的布局

应用可以包含嵌套的布局。 一个组件可以引用又引用另一个布局的布局。 例如，可以使用嵌套的布局以反映多级别的菜单结构。

下面的代码示例演示如何使用嵌套的布局。 *EpisodesComponent.cshtml*文件是要显示的组件。 请注意，该组件会引用布局`MasterListLayout`。

*EpisodesComponent.cshtml*:

```cshtml
@layout MasterListLayout
@page "/master-list/episodes"

<h1>Episodes</h1>
```

*MasterListLayout.cshtml*文件提供了`MasterListLayout`。 布局引用另一个布局`MasterLayout`，它要嵌入。

*MasterListLayout.cshtml*:

```cshtml
@layout MasterLayout
@inherits LayoutComponentBase

<nav>
    <!-- Menu structure of master list -->
    ...
</nav>

@Body
```

最后，`MasterLayout`包含的顶级布局元素，如页眉、 页脚和主菜单。

*MasterLayout.cshtml*:

```cshtml
@inherits LayoutComponentBase

<header>...</header>
<nav>...</nav>

@Body
```
