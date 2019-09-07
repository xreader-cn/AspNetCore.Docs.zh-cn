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
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="1f730-103">ASP.NET Core Blazor 布局</span><span class="sxs-lookup"><span data-stu-id="1f730-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="1f730-104">作者： [Rainer Stropek](https://www.timecockpit.com)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1f730-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1f730-105">某些应用元素（例如菜单、版权消息和公司徽标）通常是应用整体布局的一部分，并由应用中的每个组件使用。</span><span class="sxs-lookup"><span data-stu-id="1f730-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="1f730-106">每次元素需要更新时，将这些元素的代码复制到应用的所有组件&mdash;并不是一种有效的方法，必须更新每个组件。</span><span class="sxs-lookup"><span data-stu-id="1f730-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="1f730-107">此类复制很难维护，并且可能会在一段时间后导致内容不一致。</span><span class="sxs-lookup"><span data-stu-id="1f730-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="1f730-108">*布局*解决了此问题。</span><span class="sxs-lookup"><span data-stu-id="1f730-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="1f730-109">从技术上讲，布局只是另一个组件。</span><span class="sxs-lookup"><span data-stu-id="1f730-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="1f730-110">布局在 Razor 模板或代码中C#定义，可以使用[数据绑定](xref:blazor/components#data-binding)、[依赖关系注入](xref:blazor/dependency-injection)和其他组件方案。</span><span class="sxs-lookup"><span data-stu-id="1f730-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="1f730-111">若要将*组件*转换为*布局*，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1f730-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="1f730-112">继承自`LayoutComponentBase`，后者`Body`定义布局内呈现的内容的属性。</span><span class="sxs-lookup"><span data-stu-id="1f730-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="1f730-113">使用 Razor 语法`@Body`指定布局标记中呈现内容的位置。</span><span class="sxs-lookup"><span data-stu-id="1f730-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="1f730-114">下面的代码示例显示了布局组件*MainLayout*的 Razor 模板。</span><span class="sxs-lookup"><span data-stu-id="1f730-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="1f730-115">布局在导航`LayoutComponentBase`栏和页脚`@Body`之间继承和设置：</span><span class="sxs-lookup"><span data-stu-id="1f730-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="1f730-116">在基于一个 Blazor 应用程序模板的应用程序中， `MainLayout`组件（*MainLayout*）位于应用程序的*共享*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1f730-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="1f730-117">默认布局</span><span class="sxs-lookup"><span data-stu-id="1f730-117">Default layout</span></span>

<span data-ttu-id="1f730-118">在应用程序的*app.config*文件中`Router`的组件中指定默认的应用布局。</span><span class="sxs-lookup"><span data-stu-id="1f730-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="1f730-119">以下`Router`由默认 Blazor 模板提供的组件将默认布局设置`MainLayout`为组件：</span><span class="sxs-lookup"><span data-stu-id="1f730-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="1f730-120">若要为`NotFound`内容提供默认布局，请`LayoutView`指定`NotFound`内容的：</span><span class="sxs-lookup"><span data-stu-id="1f730-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="1f730-121">有关`Router`组件的详细信息，请参阅<xref:blazor/routing>。</span><span class="sxs-lookup"><span data-stu-id="1f730-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="1f730-122">在组件中指定布局</span><span class="sxs-lookup"><span data-stu-id="1f730-122">Specify a layout in a component</span></span>

<span data-ttu-id="1f730-123">使用 Razor 指令`@layout`将布局应用于组件。</span><span class="sxs-lookup"><span data-stu-id="1f730-123">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="1f730-124">编译器将转换`@layout` `LayoutAttribute`为，它将应用于组件类。</span><span class="sxs-lookup"><span data-stu-id="1f730-124">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="1f730-125">以下`MasterList`组件的内容将插入`MasterLayout`到中的位置`@Body`：</span><span class="sxs-lookup"><span data-stu-id="1f730-125">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a><span data-ttu-id="1f730-126">集中式布局选择</span><span class="sxs-lookup"><span data-stu-id="1f730-126">Centralized layout selection</span></span>

<span data-ttu-id="1f730-127">应用的每个文件夹都可以选择包含名为 *_Imports*的模板文件。</span><span class="sxs-lookup"><span data-stu-id="1f730-127">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="1f730-128">编译器将导入文件中指定的指令包含在同一文件夹中的所有 Razor 模板中，并以递归方式包含在其所有子文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1f730-128">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="1f730-129">因此， *_Imports*文件包含`@layout MyCoolLayout`可确保文件夹中的所有组件都使用。 `MyCoolLayout`</span><span class="sxs-lookup"><span data-stu-id="1f730-129">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="1f730-130">无需重复添加`@layout MyCoolLayout`到文件夹和子文件夹中的所有*razor*文件。</span><span class="sxs-lookup"><span data-stu-id="1f730-130">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="1f730-131">`@using`指令也以相同的方式应用于组件。</span><span class="sxs-lookup"><span data-stu-id="1f730-131">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="1f730-132">以下 *_Imports*文件导入：</span><span class="sxs-lookup"><span data-stu-id="1f730-132">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="1f730-133">`MyCoolLayout`。</span><span class="sxs-lookup"><span data-stu-id="1f730-133">`MyCoolLayout`.</span></span>
* <span data-ttu-id="1f730-134">同一文件夹和所有子文件夹中的所有 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="1f730-134">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="1f730-135">`BlazorApp1.Data` 命名空间。</span><span class="sxs-lookup"><span data-stu-id="1f730-135">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="1f730-136">*_Imports*文件类似于[razor 视图和页面的 _ViewImports 文件](xref:mvc/views/layout#importing-shared-directives)，但专门应用于 razor 组件文件。</span><span class="sxs-lookup"><span data-stu-id="1f730-136">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="1f730-137">嵌套布局</span><span class="sxs-lookup"><span data-stu-id="1f730-137">Nested layouts</span></span>

<span data-ttu-id="1f730-138">应用可由嵌套布局组成。</span><span class="sxs-lookup"><span data-stu-id="1f730-138">Apps can consist of nested layouts.</span></span> <span data-ttu-id="1f730-139">组件可以引用另一个布局。</span><span class="sxs-lookup"><span data-stu-id="1f730-139">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="1f730-140">例如，使用嵌套布局创建多层菜单结构。</span><span class="sxs-lookup"><span data-stu-id="1f730-140">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="1f730-141">下面的示例演示如何使用嵌套的布局。</span><span class="sxs-lookup"><span data-stu-id="1f730-141">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="1f730-142">*EpisodesComponent*文件是要显示的组件。</span><span class="sxs-lookup"><span data-stu-id="1f730-142">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="1f730-143">组件引用`MasterListLayout`：</span><span class="sxs-lookup"><span data-stu-id="1f730-143">The component references the `MasterListLayout`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="1f730-144">*MasterListLayout*文件提供`MasterListLayout`。</span><span class="sxs-lookup"><span data-stu-id="1f730-144">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="1f730-145">布局引用在呈现它时`MasterLayout`的另一个布局。</span><span class="sxs-lookup"><span data-stu-id="1f730-145">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="1f730-146">`EpisodesComponent`在出现以下`@Body`情况时呈现：</span><span class="sxs-lookup"><span data-stu-id="1f730-146">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="1f730-147">最后， `MasterLayout`在*MasterLayout*中包含顶级布局元素，如标头、主菜单和脚注。</span><span class="sxs-lookup"><span data-stu-id="1f730-147">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="1f730-148">`MasterListLayout`在出现的情况下`@Body`呈现： `EpisodesComponent`</span><span class="sxs-lookup"><span data-stu-id="1f730-148">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a><span data-ttu-id="1f730-149">其他资源</span><span class="sxs-lookup"><span data-stu-id="1f730-149">Additional resources</span></span>

* <xref:mvc/views/layout>
