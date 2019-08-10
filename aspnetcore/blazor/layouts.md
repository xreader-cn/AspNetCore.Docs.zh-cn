---
title: ASP.NET Core Blazor 布局
author: guardrex
description: 了解如何为 Blazor 应用创建可重用的布局组件。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/layouts
ms.openlocfilehash: 2d652e149381f0a93e3135da978ab5737d47c6f1
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "68948217"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="995ec-103">ASP.NET Core Blazor 布局</span><span class="sxs-lookup"><span data-stu-id="995ec-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="995ec-104">作者: [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="995ec-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="995ec-105">某些应用元素 (例如菜单、版权消息和公司徽标) 通常是应用整体布局的一部分, 并由应用中的每个组件使用。</span><span class="sxs-lookup"><span data-stu-id="995ec-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="995ec-106">每次元素需要更新时, 将这些元素的代码复制到应用的所有组件&mdash;并不是一种有效的方法, 必须更新每个组件。</span><span class="sxs-lookup"><span data-stu-id="995ec-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="995ec-107">此类复制很难维护, 并且可能会在一段时间后导致内容不一致。</span><span class="sxs-lookup"><span data-stu-id="995ec-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="995ec-108">*布局*解决了此问题。</span><span class="sxs-lookup"><span data-stu-id="995ec-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="995ec-109">从技术上讲, 布局只是另一个组件。</span><span class="sxs-lookup"><span data-stu-id="995ec-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="995ec-110">布局在 Razor 模板或代码中C#定义, 可以使用[数据绑定](xref:blazor/components#data-binding)、[依赖关系注入](xref:blazor/dependency-injection)和其他组件方案。</span><span class="sxs-lookup"><span data-stu-id="995ec-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="995ec-111">若要将*组件*转换为*布局*, 请执行以下操作:</span><span class="sxs-lookup"><span data-stu-id="995ec-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="995ec-112">继承自`LayoutComponentBase`, 后者`Body`定义布局内呈现的内容的属性。</span><span class="sxs-lookup"><span data-stu-id="995ec-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="995ec-113">使用 Razor 语法`@Body`指定布局标记中呈现内容的位置。</span><span class="sxs-lookup"><span data-stu-id="995ec-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="995ec-114">下面的代码示例显示了布局组件*MainLayout*的 Razor 模板。</span><span class="sxs-lookup"><span data-stu-id="995ec-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="995ec-115">布局在导航`LayoutComponentBase`栏和页脚`@Body`之间继承和设置:</span><span class="sxs-lookup"><span data-stu-id="995ec-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="995ec-116">在组件中指定布局</span><span class="sxs-lookup"><span data-stu-id="995ec-116">Specify a layout in a component</span></span>

<span data-ttu-id="995ec-117">使用 Razor 指令`@layout`将布局应用于组件。</span><span class="sxs-lookup"><span data-stu-id="995ec-117">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="995ec-118">编译器将转换`@layout` `LayoutAttribute`为, 它将应用于组件类。</span><span class="sxs-lookup"><span data-stu-id="995ec-118">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="995ec-119">以下组件*MasterList*的内容将插入到`MainLayout`中的位置: `@Body`</span><span class="sxs-lookup"><span data-stu-id="995ec-119">The content of the following component, *MasterList.razor*, is inserted into the `MainLayout` at the position of `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a><span data-ttu-id="995ec-120">集中式布局选择</span><span class="sxs-lookup"><span data-stu-id="995ec-120">Centralized layout selection</span></span>

<span data-ttu-id="995ec-121">应用的每个文件夹都可以选择包含名为 *_Imports*的模板文件。</span><span class="sxs-lookup"><span data-stu-id="995ec-121">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="995ec-122">编译器将导入文件中指定的指令包含在同一文件夹中的所有 Razor 模板中, 并以递归方式包含在其所有子文件夹中。</span><span class="sxs-lookup"><span data-stu-id="995ec-122">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="995ec-123">因此, *_Imports*文件包含`@layout MainLayout`可确保文件夹中的所有组件都使用。 `MainLayout`</span><span class="sxs-lookup"><span data-stu-id="995ec-123">Therefore, an *_Imports.razor* file containing `@layout MainLayout` ensures that all of the components in a folder use `MainLayout`.</span></span> <span data-ttu-id="995ec-124">无需重复添加`@layout MainLayout`到文件夹和子文件夹中的所有*razor*文件。</span><span class="sxs-lookup"><span data-stu-id="995ec-124">There's no need to repeatedly add `@layout MainLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="995ec-125">`@using`指令也以相同的方式应用于组件。</span><span class="sxs-lookup"><span data-stu-id="995ec-125">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="995ec-126">以下 *_Imports*文件导入:</span><span class="sxs-lookup"><span data-stu-id="995ec-126">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="995ec-127">`MainLayout`。</span><span class="sxs-lookup"><span data-stu-id="995ec-127">`MainLayout`.</span></span>
* <span data-ttu-id="995ec-128">同一文件夹和所有子文件夹中的所有 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="995ec-128">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="995ec-129">`BlazorApp1.Data` 命名空间。</span><span class="sxs-lookup"><span data-stu-id="995ec-129">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="995ec-130">*_Imports*文件类似于[razor 视图和页面的 _ViewImports 文件](xref:mvc/views/layout#importing-shared-directives), 但专门应用于 razor 组件文件。</span><span class="sxs-lookup"><span data-stu-id="995ec-130">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="995ec-131">对于布局选择, Blazor 模板使用 *_Imports*文件。</span><span class="sxs-lookup"><span data-stu-id="995ec-131">The Blazor templates use *_Imports.razor* files for layout selection.</span></span> <span data-ttu-id="995ec-132">通过 Blazor 模板创建的应用将包含项目根目录和*Pages*文件夹中的 *_Imports*文件。</span><span class="sxs-lookup"><span data-stu-id="995ec-132">An app created from a Blazor template contains the *_Imports.razor* file in the root of the project and in the *Pages* folder.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="995ec-133">嵌套布局</span><span class="sxs-lookup"><span data-stu-id="995ec-133">Nested layouts</span></span>

<span data-ttu-id="995ec-134">应用可由嵌套布局组成。</span><span class="sxs-lookup"><span data-stu-id="995ec-134">Apps can consist of nested layouts.</span></span> <span data-ttu-id="995ec-135">组件可以引用另一个布局。</span><span class="sxs-lookup"><span data-stu-id="995ec-135">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="995ec-136">例如, 使用嵌套布局创建多层菜单结构。</span><span class="sxs-lookup"><span data-stu-id="995ec-136">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="995ec-137">下面的示例演示如何使用嵌套的布局。</span><span class="sxs-lookup"><span data-stu-id="995ec-137">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="995ec-138">*EpisodesComponent*文件是要显示的组件。</span><span class="sxs-lookup"><span data-stu-id="995ec-138">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="995ec-139">组件引用`MasterListLayout`:</span><span class="sxs-lookup"><span data-stu-id="995ec-139">The component references the `MasterListLayout`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="995ec-140">*MasterListLayout*文件提供`MasterListLayout`。</span><span class="sxs-lookup"><span data-stu-id="995ec-140">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="995ec-141">布局引用在呈现它时`MasterLayout`的另一个布局。</span><span class="sxs-lookup"><span data-stu-id="995ec-141">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="995ec-142">`EpisodesComponent`在出现以下`@Body`情况时呈现:</span><span class="sxs-lookup"><span data-stu-id="995ec-142">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="995ec-143">最后, `MasterLayout`在*MasterLayout*中包含顶级布局元素, 如标头、主菜单和脚注。</span><span class="sxs-lookup"><span data-stu-id="995ec-143">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="995ec-144">`MasterListLayout`在出现的情况`@Body`下呈现: `EpisodesComponent`</span><span class="sxs-lookup"><span data-stu-id="995ec-144">`MasterListLayout` with `EpisodesComponent` are rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a><span data-ttu-id="995ec-145">其他资源</span><span class="sxs-lookup"><span data-stu-id="995ec-145">Additional resources</span></span>

* <xref:mvc/views/layout>
