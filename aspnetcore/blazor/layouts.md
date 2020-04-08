---
title: ASP.NET Core Blazor 布局
author: guardrex
description: 了解如何为 Blazor 应用创建可重用布局组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647922"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a><span data-ttu-id="7030c-103">ASP.NET Core Blazor 布局</span><span class="sxs-lookup"><span data-stu-id="7030c-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="7030c-104">作者：[Rainer Stropek](https://www.timecockpit.com) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7030c-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7030c-105">有些应用元素（例如菜单、版权消息和公司徽标）通常是应用整体布局的一部分，并被应用中的每个组件使用。</span><span class="sxs-lookup"><span data-stu-id="7030c-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="7030c-106">将这些元素的代码复制到应用的所有组件中并不是高效的方法 &mdash; 每当其中一个元素需要更新，每个组件都必须更新。</span><span class="sxs-lookup"><span data-stu-id="7030c-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="7030c-107">此类复制难以维护，并会随时间推移导致内容不一致。</span><span class="sxs-lookup"><span data-stu-id="7030c-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="7030c-108">*布局*可解决此问题。</span><span class="sxs-lookup"><span data-stu-id="7030c-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="7030c-109">从技术上讲，布局也是一个组件。</span><span class="sxs-lookup"><span data-stu-id="7030c-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="7030c-110">布局在 Razor 模板或 C# 代码中定义，并可使用[数据绑定](xref:blazor/data-binding)、[依赖项注入](xref:blazor/dependency-injection)和其他组件方案。</span><span class="sxs-lookup"><span data-stu-id="7030c-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="7030c-111">若要将*组件*转换为*布局*，该组件应：</span><span class="sxs-lookup"><span data-stu-id="7030c-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="7030c-112">继承自 `LayoutComponentBase`，后者为布局内的呈现内容定义 `Body` 属性。</span><span class="sxs-lookup"><span data-stu-id="7030c-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="7030c-113">使用 Razor 语法 `@Body` 在布局标记中指定呈现内容的位置。</span><span class="sxs-lookup"><span data-stu-id="7030c-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="7030c-114">以下代码示例显示布局组件 *MainLayout.razor* 的 Razor 模板。</span><span class="sxs-lookup"><span data-stu-id="7030c-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="7030c-115">布局继承 `LayoutComponentBase` 并在导航栏和页脚之间设置 `@Body`：</span><span class="sxs-lookup"><span data-stu-id="7030c-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="7030c-116">在基于其中一个 Blazor 应用模板的应用中，`MainLayout` 组件 (*MainLayout.razor*) 位于应用的 *Shared* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="7030c-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="7030c-117">默认布局</span><span class="sxs-lookup"><span data-stu-id="7030c-117">Default layout</span></span>

<span data-ttu-id="7030c-118">在应用的 `Router`App.razor*文件的* 组件中指定默认应用布局。</span><span class="sxs-lookup"><span data-stu-id="7030c-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="7030c-119">默认 `Router` 模板提供的以下 Blazor 组件将默认布局设置为 `MainLayout` 组件：</span><span class="sxs-lookup"><span data-stu-id="7030c-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="7030c-120">若要为 `NotFound` 内容提供默认布局，请为 `LayoutView` 内容指定 `NotFound`：</span><span class="sxs-lookup"><span data-stu-id="7030c-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="7030c-121">有关 `Router` 组件的详细信息，请参阅 <xref:blazor/routing>。</span><span class="sxs-lookup"><span data-stu-id="7030c-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="7030c-122">在路由器中将布局指定为默认布局是一种有用的做法，因为可以按组件或按文件夹进行替代。</span><span class="sxs-lookup"><span data-stu-id="7030c-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="7030c-123">最好使用路由器来设置应用的默认布局，因为这是最常规的方法。</span><span class="sxs-lookup"><span data-stu-id="7030c-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="7030c-124">在组件中指定布局</span><span class="sxs-lookup"><span data-stu-id="7030c-124">Specify a layout in a component</span></span>

<span data-ttu-id="7030c-125">使用 Razor 指令 `@layout` 将布局应用于组件。</span><span class="sxs-lookup"><span data-stu-id="7030c-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="7030c-126">编译器将 `@layout` 转换为 `LayoutAttribute`，后者应用于组件类。</span><span class="sxs-lookup"><span data-stu-id="7030c-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="7030c-127">以下 `MasterList` 组件的内容插入到 `MasterLayout` 中 `@Body` 的位置：</span><span class="sxs-lookup"><span data-stu-id="7030c-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="7030c-128">直接在组件中指定布局会替代路由器中设置的*默认布局*或从 `@layout`_Imports.razor*导入的* 指令。</span><span class="sxs-lookup"><span data-stu-id="7030c-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="7030c-129">集中式布局选择</span><span class="sxs-lookup"><span data-stu-id="7030c-129">Centralized layout selection</span></span>

<span data-ttu-id="7030c-130">应用的每个文件夹都可以选择包含名为 *_Imports.razor* 的模板文件。</span><span class="sxs-lookup"><span data-stu-id="7030c-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="7030c-131">编译器将导入文件中指定的指令包括在同一文件夹中的所有 Razor 模板内，并在其所有子文件夹中以递归方式包括。</span><span class="sxs-lookup"><span data-stu-id="7030c-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="7030c-132">因此，包含 *的*_Imports.razor`@layout MyCoolLayout` 文件可确保文件夹中的所有组件都使用 `MyCoolLayout`。</span><span class="sxs-lookup"><span data-stu-id="7030c-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="7030c-133">无需将 `@layout MyCoolLayout` 重复添加到文件夹和子文件夹内的所有 *.razor* 文件。</span><span class="sxs-lookup"><span data-stu-id="7030c-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="7030c-134">`@using` 指令也以相同的方式应用于组件。</span><span class="sxs-lookup"><span data-stu-id="7030c-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="7030c-135">以下 *_Imports.razor* 文件导入：</span><span class="sxs-lookup"><span data-stu-id="7030c-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="7030c-136">`MyCoolLayout`</span><span class="sxs-lookup"><span data-stu-id="7030c-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="7030c-137">同一文件夹以及任何子文件夹中的所有 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="7030c-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="7030c-138">`BlazorApp1.Data` 命名空间。</span><span class="sxs-lookup"><span data-stu-id="7030c-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="7030c-139">*_Imports.razor* 文件类似于 [Razor 视图和页面的 _ViewImports.cshtml 文件](xref:mvc/views/layout#importing-shared-directives)，但专门应用于 Razor 组件文件。</span><span class="sxs-lookup"><span data-stu-id="7030c-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="7030c-140">在 *_Imports.razor* 中指定布局会替代指定为路由器*默认布局*的布局。</span><span class="sxs-lookup"><span data-stu-id="7030c-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="7030c-141">嵌套布局</span><span class="sxs-lookup"><span data-stu-id="7030c-141">Nested layouts</span></span>

<span data-ttu-id="7030c-142">应用可以包含嵌套布局。</span><span class="sxs-lookup"><span data-stu-id="7030c-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="7030c-143">组件可以引用一个布局，该布局反过来引用另一个布局。</span><span class="sxs-lookup"><span data-stu-id="7030c-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="7030c-144">例如，嵌套布局用于创建多级菜单结构。</span><span class="sxs-lookup"><span data-stu-id="7030c-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="7030c-145">以下示例演示如何使用嵌套布局。</span><span class="sxs-lookup"><span data-stu-id="7030c-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="7030c-146">*EpisodesComponent.razor* 文件是要显示的组件。</span><span class="sxs-lookup"><span data-stu-id="7030c-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="7030c-147">该组件引用 `MasterListLayout`：</span><span class="sxs-lookup"><span data-stu-id="7030c-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="7030c-148">*MasterListLayout.razor* 文件提供 `MasterListLayout`。</span><span class="sxs-lookup"><span data-stu-id="7030c-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="7030c-149">该布局引用另一个布局 `MasterLayout` 并在其中呈现。</span><span class="sxs-lookup"><span data-stu-id="7030c-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="7030c-150">`EpisodesComponent` 在显示 `@Body` 的位置呈现：</span><span class="sxs-lookup"><span data-stu-id="7030c-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="7030c-151">最后，`MasterLayout`MasterLayout.razor*中的* 包含顶级布局元素，例如页眉、主菜单和页脚。</span><span class="sxs-lookup"><span data-stu-id="7030c-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="7030c-152">具有 `MasterListLayout` 的 `EpisodesComponent` 在 `@Body` 显示的位置呈现：</span><span class="sxs-lookup"><span data-stu-id="7030c-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="7030c-153">与集成组件共享 Razor Pages 布局</span><span class="sxs-lookup"><span data-stu-id="7030c-153">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="7030c-154">当可路由组件集成到 Razor Pages 应用中时，该应用的共享布局可与这些组件配合使用。</span><span class="sxs-lookup"><span data-stu-id="7030c-154">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="7030c-155">有关更多信息，请参见 <xref:blazor/integrate-components>。</span><span class="sxs-lookup"><span data-stu-id="7030c-155">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7030c-156">其他资源</span><span class="sxs-lookup"><span data-stu-id="7030c-156">Additional resources</span></span>

* <xref:mvc/views/layout>
