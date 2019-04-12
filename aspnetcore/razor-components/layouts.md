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
# <a name="razor-components-layouts"></a><span data-ttu-id="c14a8-103">Razor 组件布局</span><span class="sxs-lookup"><span data-stu-id="c14a8-103">Razor Components layouts</span></span>

<span data-ttu-id="c14a8-104">通过[Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="c14a8-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="c14a8-105">应用程序通常包含多个组件。</span><span class="sxs-lookup"><span data-stu-id="c14a8-105">Apps typically contain more than one component.</span></span> <span data-ttu-id="c14a8-106">布局元素，如菜单、 版权消息和徽标，必须在所有组件上存在。</span><span class="sxs-lookup"><span data-stu-id="c14a8-106">Layout elements, such as menus, copyright messages, and logos, must be present on all components.</span></span> <span data-ttu-id="c14a8-107">将这些布局元素的代码复制到的所有应用程序组件并不是有效的途径。</span><span class="sxs-lookup"><span data-stu-id="c14a8-107">Copying the code of these layout elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="c14a8-108">此类重复难以维护，并随着时间的推移可能导致不一致的内容。</span><span class="sxs-lookup"><span data-stu-id="c14a8-108">Such duplication is hard to maintain and probably leads to inconsistent content over time.</span></span> <span data-ttu-id="c14a8-109">*布局*解决此问题。</span><span class="sxs-lookup"><span data-stu-id="c14a8-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="c14a8-110">从技术上讲，布局是只是另一个组件。</span><span class="sxs-lookup"><span data-stu-id="c14a8-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="c14a8-111">布局定义 Razor 模板中或在C#代码，并可以包含[数据绑定](xref:razor-components/components#data-binding)，[依赖关系注入](xref:razor-components/dependency-injection)，和其他普通功能的组件。</span><span class="sxs-lookup"><span data-stu-id="c14a8-111">A layout is defined in a Razor template or in C# code and can contain [data binding](xref:razor-components/components#data-binding), [dependency injection](xref:razor-components/dependency-injection), and other ordinary features of components.</span></span>

<span data-ttu-id="c14a8-112">打开两个其他方面*组件*到*布局*</span><span class="sxs-lookup"><span data-stu-id="c14a8-112">Two additional aspects turn a *component* into a *layout*</span></span>

* <span data-ttu-id="c14a8-113">布局组件必须继承自`LayoutComponentBase`。</span><span class="sxs-lookup"><span data-stu-id="c14a8-113">The layout component must inherit from `LayoutComponentBase`.</span></span> `LayoutComponentBase` <span data-ttu-id="c14a8-114">定义`Body`属性，其中包含布局内呈现的内容。</span><span class="sxs-lookup"><span data-stu-id="c14a8-114">defines a `Body` property that contains the content to be rendered inside the layout.</span></span>
* <span data-ttu-id="c14a8-115">布局组件使用`Body`属性指定的正文内容应呈现使用 Razor 语法`@Body`。</span><span class="sxs-lookup"><span data-stu-id="c14a8-115">The layout component uses the `Body` property to specify where the body content should be rendered using the Razor syntax `@Body`.</span></span> <span data-ttu-id="c14a8-116">在呈现期间`@Body`布局的内容替换为。</span><span class="sxs-lookup"><span data-stu-id="c14a8-116">During rendering, `@Body` is replaced by the content of the layout.</span></span>

<span data-ttu-id="c14a8-117">下面的代码示例显示了布局组件的 Razor 模板。</span><span class="sxs-lookup"><span data-stu-id="c14a8-117">The following code sample shows the Razor template of a layout component.</span></span> <span data-ttu-id="c14a8-118">请注意，使用`LayoutComponentBase`和`@Body`:</span><span class="sxs-lookup"><span data-stu-id="c14a8-118">Note the use of `LayoutComponentBase` and `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.cshtml)]

## <a name="use-a-layout-in-a-component"></a><span data-ttu-id="c14a8-119">在组件中使用的布局</span><span class="sxs-lookup"><span data-stu-id="c14a8-119">Use a layout in a component</span></span>

<span data-ttu-id="c14a8-120">使用 Razor 指令`@layout`要应用于组件的布局。</span><span class="sxs-lookup"><span data-stu-id="c14a8-120">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="c14a8-121">编译器将转换到此指令`LayoutAttribute`，其应用到 component 类。</span><span class="sxs-lookup"><span data-stu-id="c14a8-121">The compiler converts this directive into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="c14a8-122">下面的代码示例演示了这一概念。</span><span class="sxs-lookup"><span data-stu-id="c14a8-122">The following code sample demonstrates the concept.</span></span> <span data-ttu-id="c14a8-123">此组件的内容插入到*MasterLayout*的位置处`@Body`:</span><span class="sxs-lookup"><span data-stu-id="c14a8-123">The content of this component is inserted into the *MasterLayout* at the position of `@Body`:</span></span>

```cshtml
@layout MasterLayout
@page "/master-list"

<h2>Master Episode List</h2>
```

## <a name="centralized-layout-selection"></a><span data-ttu-id="c14a8-124">选择集中式的布局</span><span class="sxs-lookup"><span data-stu-id="c14a8-124">Centralized layout selection</span></span>

<span data-ttu-id="c14a8-125">每个文件夹的应用程序可以选择包含名为的模板文件 *_ViewImports.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="c14a8-125">Every folder of a an app can optionally contain a template file named *_ViewImports.cshtml*.</span></span> <span data-ttu-id="c14a8-126">编译器包含在同一文件夹中的 Razor 模板和以递归方式在所有子文件夹中的所有视图导入文件中指定的指令。</span><span class="sxs-lookup"><span data-stu-id="c14a8-126">The compiler includes the directives specified in the view imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="c14a8-127">因此， *_ViewImports.cshtml*文件中含有`@layout MainLayout`确保所有文件夹，请使用中的组件*MainLayout*布局。</span><span class="sxs-lookup"><span data-stu-id="c14a8-127">Therefore, a *_ViewImports.cshtml* file containing `@layout MainLayout` ensures that all of the components in a folder use the *MainLayout* layout.</span></span> <span data-ttu-id="c14a8-128">无需反复添加`@layout`的所有 *.razor*文件。</span><span class="sxs-lookup"><span data-stu-id="c14a8-128">There's no need to repeatedly add `@layout` to all of the *.razor* files.</span></span>

<span data-ttu-id="c14a8-129">请注意，使用默认模板 *_ViewImports.cshtml*机制，用于选择布局。</span><span class="sxs-lookup"><span data-stu-id="c14a8-129">Note that the default template uses the *_ViewImports.cshtml* mechanism for layout selection.</span></span> <span data-ttu-id="c14a8-130">新创建的应用程序包含 *_ViewImports.cshtml*中的文件*组件/页*文件夹。</span><span class="sxs-lookup"><span data-stu-id="c14a8-130">A newly created app contains the *_ViewImports.cshtml* file in the *Components/Pages* folder.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="c14a8-131">嵌套的布局</span><span class="sxs-lookup"><span data-stu-id="c14a8-131">Nested layouts</span></span>

<span data-ttu-id="c14a8-132">应用可以包含嵌套的布局。</span><span class="sxs-lookup"><span data-stu-id="c14a8-132">Apps can consist of nested layouts.</span></span> <span data-ttu-id="c14a8-133">一个组件可以引用又引用另一个布局的布局。</span><span class="sxs-lookup"><span data-stu-id="c14a8-133">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="c14a8-134">例如，可以使用嵌套的布局以反映多级别的菜单结构。</span><span class="sxs-lookup"><span data-stu-id="c14a8-134">For example, nesting layouts can be used to reflect a multi-level menu structure.</span></span>

<span data-ttu-id="c14a8-135">下面的代码示例演示如何使用嵌套的布局。</span><span class="sxs-lookup"><span data-stu-id="c14a8-135">The following code samples show how to use nested layouts.</span></span> <span data-ttu-id="c14a8-136">*EpisodesComponent.cshtml*文件是要显示的组件。</span><span class="sxs-lookup"><span data-stu-id="c14a8-136">The *EpisodesComponent.cshtml* file is the component to display.</span></span> <span data-ttu-id="c14a8-137">请注意，该组件会引用布局`MasterListLayout`。</span><span class="sxs-lookup"><span data-stu-id="c14a8-137">Note that the component references the layout `MasterListLayout`.</span></span>

<span data-ttu-id="c14a8-138">*EpisodesComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c14a8-138">*EpisodesComponent.cshtml*:</span></span>

```cshtml
@layout MasterListLayout
@page "/master-list/episodes"

<h1>Episodes</h1>
```

<span data-ttu-id="c14a8-139">*MasterListLayout.cshtml*文件提供了`MasterListLayout`。</span><span class="sxs-lookup"><span data-stu-id="c14a8-139">The *MasterListLayout.cshtml* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="c14a8-140">布局引用另一个布局`MasterLayout`，它要嵌入。</span><span class="sxs-lookup"><span data-stu-id="c14a8-140">The layout references another layout, `MasterLayout`, where it's going to be embedded.</span></span>

<span data-ttu-id="c14a8-141">*MasterListLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c14a8-141">*MasterListLayout.cshtml*:</span></span>

```cshtml
@layout MasterLayout
@inherits LayoutComponentBase

<nav>
    <!-- Menu structure of master list -->
    ...
</nav>

@Body
```

<span data-ttu-id="c14a8-142">最后，`MasterLayout`包含的顶级布局元素，如页眉、 页脚和主菜单。</span><span class="sxs-lookup"><span data-stu-id="c14a8-142">Finally, `MasterLayout` contains the top-level layout elements, such as the header, footer, and main menu.</span></span>

<span data-ttu-id="c14a8-143">*MasterLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c14a8-143">*MasterLayout.cshtml*:</span></span>

```cshtml
@inherits LayoutComponentBase

<header>...</header>
<nav>...</nav>

@Body
```
