---
title: ASP.NET Core Blazor 布局
author: guardrex
description: 了解如何为 Blazor 应用创建可重用布局组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 3cb7c6184c13a003b4f4294f887d8938caa42f97
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506898"
---
# <a name="aspnet-core-no-locblazor-layouts"></a><span data-ttu-id="34332-103">ASP.NET Core Blazor 布局</span><span class="sxs-lookup"><span data-stu-id="34332-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="34332-104">作者：[Rainer Stropek](https://www.timecockpit.com) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="34332-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="34332-105">有些应用元素（例如菜单、版权消息和公司徽标）通常是应用整体布局的一部分，并被应用中的每个组件使用。</span><span class="sxs-lookup"><span data-stu-id="34332-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="34332-106">将这些元素的代码复制到应用的所有组件并不是一种有效的方法。</span><span class="sxs-lookup"><span data-stu-id="34332-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="34332-107">每当一个元素需要更新时，每个组件都必须更新。</span><span class="sxs-lookup"><span data-stu-id="34332-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="34332-108">此类复制难以维护，并会随时间推移导致内容不一致。</span><span class="sxs-lookup"><span data-stu-id="34332-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="34332-109">*布局* 可解决此问题。</span><span class="sxs-lookup"><span data-stu-id="34332-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="34332-110">从技术上讲，布局也是一个组件。</span><span class="sxs-lookup"><span data-stu-id="34332-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="34332-111">布局在 Razor 模板或 C# 代码中定义，并可使用[数据绑定](xref:blazor/components/data-binding)、[依赖项注入](xref:blazor/fundamentals/dependency-injection)和其他组件方案。</span><span class="sxs-lookup"><span data-stu-id="34332-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="34332-112">将组件转换为布局：</span><span class="sxs-lookup"><span data-stu-id="34332-112">To convert a component into a layout:</span></span>

* <span data-ttu-id="34332-113">组件继承自 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>。</span><span class="sxs-lookup"><span data-stu-id="34332-113">Inherit the component from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>.</span></span> <span data-ttu-id="34332-114"><xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 为布局内的呈现内容定义 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 属性。</span><span class="sxs-lookup"><span data-stu-id="34332-114">The <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="34332-115">使用 Razor 语法 `@Body` 在布局标记中指定呈现内容的位置。</span><span class="sxs-lookup"><span data-stu-id="34332-115">Use the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="34332-116">以下代码示例显示布局组件 `MainLayout.razor` 的 Razor 模板。</span><span class="sxs-lookup"><span data-stu-id="34332-116">The following code sample shows the Razor template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="34332-117">布局继承 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 并在导航栏和页脚之间设置 `@Body`：</span><span class="sxs-lookup"><span data-stu-id="34332-117">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a><span data-ttu-id="34332-118">`MainLayout` 组件</span><span class="sxs-lookup"><span data-stu-id="34332-118">`MainLayout` component</span></span>

<span data-ttu-id="34332-119">在基于其中一个 Blazor 项目模板的应用中，`MainLayout` 组件 (`MainLayout.razor`) 位于应用的“`Shared`”文件夹中：</span><span class="sxs-lookup"><span data-stu-id="34332-119">In an app based on one of the Blazor project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a><span data-ttu-id="34332-120">默认布局</span><span class="sxs-lookup"><span data-stu-id="34332-120">Default layout</span></span>

<span data-ttu-id="34332-121">在应用的 `App.razor` 文件的 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件中指定默认应用布局。</span><span class="sxs-lookup"><span data-stu-id="34332-121">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="34332-122">默认 Blazor 模板提供的以下 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件将默认布局设置为 `MainLayout` 组件：</span><span class="sxs-lookup"><span data-stu-id="34332-122">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="34332-123">若要为 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 内容提供默认布局，请为 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 内容指定 <xref:Microsoft.AspNetCore.Components.LayoutView>：</span><span class="sxs-lookup"><span data-stu-id="34332-123">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="34332-124">有关 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件的详细信息，请参阅 <xref:blazor/fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="34332-124">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="34332-125">在路由器中将布局指定为默认布局是一种有用的做法，因为可以按组件或按文件夹进行替代。</span><span class="sxs-lookup"><span data-stu-id="34332-125">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="34332-126">最好使用路由器来设置应用的默认布局，因为这是最常规的方法。</span><span class="sxs-lookup"><span data-stu-id="34332-126">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="34332-127">在组件中指定布局</span><span class="sxs-lookup"><span data-stu-id="34332-127">Specify a layout in a component</span></span>

<span data-ttu-id="34332-128">使用 Razor 指令 `@layout` 将布局应用于组件。</span><span class="sxs-lookup"><span data-stu-id="34332-128">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="34332-129">编译器将 `@layout` 转换为 <xref:Microsoft.AspNetCore.Components.LayoutAttribute>，后者应用于组件类。</span><span class="sxs-lookup"><span data-stu-id="34332-129">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="34332-130">以下 `MasterList` 组件的内容插入到 `MasterLayout` 中 `@Body` 的位置：</span><span class="sxs-lookup"><span data-stu-id="34332-130">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="34332-131">直接在组件中指定布局会替代路由器中设置的默认布局或从 `_Imports.razor` 导入的 `@layout` 指令。</span><span class="sxs-lookup"><span data-stu-id="34332-131">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="34332-132">集中式布局选择</span><span class="sxs-lookup"><span data-stu-id="34332-132">Centralized layout selection</span></span>

<span data-ttu-id="34332-133">应用的每个文件夹都可以选择包含名为 `_Imports.razor` 的模板文件。</span><span class="sxs-lookup"><span data-stu-id="34332-133">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="34332-134">编译器将导入文件中指定的指令包括在同一文件夹中的所有 Razor 模板内，并在其所有子文件夹中以递归方式包括。</span><span class="sxs-lookup"><span data-stu-id="34332-134">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="34332-135">因此，包含 `@layout MyCoolLayout` 的 `_Imports.razor` 文件可确保文件夹中的所有组件都使用 `MyCoolLayout`。</span><span class="sxs-lookup"><span data-stu-id="34332-135">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="34332-136">无需将 `@layout MyCoolLayout` 重复添加到文件夹和子文件夹内的所有 `.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="34332-136">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="34332-137">`@using` 指令也以相同的方式应用于组件。</span><span class="sxs-lookup"><span data-stu-id="34332-137">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="34332-138">添加以下 `_Imports.razor` 文件导入内容：</span><span class="sxs-lookup"><span data-stu-id="34332-138">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="34332-139">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="34332-139">`MyCoolLayout`.</span></span>
* <span data-ttu-id="34332-140">同一文件夹以及任何子文件夹中的所有 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="34332-140">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="34332-141">`BlazorApp1.Data` 命名空间。</span><span class="sxs-lookup"><span data-stu-id="34332-141">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="34332-142">`_Imports.razor` 文件类似于 [Razor 视图和页面的 _ViewImports.cshtml 文件](xref:mvc/views/layout#importing-shared-directives)，但专门应用于 Razor 组件文件。</span><span class="sxs-lookup"><span data-stu-id="34332-142">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="34332-143">在 `_Imports.razor` 中指定布局会替代指定为路由器默认布局的布局。</span><span class="sxs-lookup"><span data-stu-id="34332-143">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout*.</span></span>

> [!WARNING]
> <span data-ttu-id="34332-144">请勿向根 `_Imports.razor` 文件添加 Razor `@layout` 指令，这会导致应用中的布局形成无限循环。</span><span class="sxs-lookup"><span data-stu-id="34332-144">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="34332-145">请在 `Router` 组件中指定布局，以控制默认应用布局。</span><span class="sxs-lookup"><span data-stu-id="34332-145">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="34332-146">有关详细信息，请参阅[默认布局](#default-layout)部分。</span><span class="sxs-lookup"><span data-stu-id="34332-146">For more information, see the [Default layout](#default-layout) section.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="34332-147">嵌套布局</span><span class="sxs-lookup"><span data-stu-id="34332-147">Nested layouts</span></span>

<span data-ttu-id="34332-148">应用可以包含嵌套布局。</span><span class="sxs-lookup"><span data-stu-id="34332-148">Apps can consist of nested layouts.</span></span> <span data-ttu-id="34332-149">组件可以引用一个布局，该布局反过来引用另一个布局。</span><span class="sxs-lookup"><span data-stu-id="34332-149">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="34332-150">例如，嵌套布局用于创建多级菜单结构。</span><span class="sxs-lookup"><span data-stu-id="34332-150">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="34332-151">以下示例演示如何使用嵌套布局。</span><span class="sxs-lookup"><span data-stu-id="34332-151">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="34332-152">`EpisodesComponent.razor` 文件是要显示的组件。</span><span class="sxs-lookup"><span data-stu-id="34332-152">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="34332-153">该组件引用 `MasterListLayout`：</span><span class="sxs-lookup"><span data-stu-id="34332-153">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="34332-154">`MasterListLayout.razor` 文件提供 `MasterListLayout`。</span><span class="sxs-lookup"><span data-stu-id="34332-154">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="34332-155">该布局引用另一个布局 `MasterLayout` 并在其中呈现。</span><span class="sxs-lookup"><span data-stu-id="34332-155">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="34332-156">`EpisodesComponent` 在显示 `@Body` 的位置呈现：</span><span class="sxs-lookup"><span data-stu-id="34332-156">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="34332-157">最后，`MasterLayout.razor` 中的 `MasterLayout` 包含顶级布局元素，例如页眉、主菜单和页脚。</span><span class="sxs-lookup"><span data-stu-id="34332-157">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="34332-158">具有 `EpisodesComponent` 的 `MasterListLayout` 在 `@Body` 显示的位置呈现：</span><span class="sxs-lookup"><span data-stu-id="34332-158">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a><span data-ttu-id="34332-159">与集成组件共享 Razor Pages 布局</span><span class="sxs-lookup"><span data-stu-id="34332-159">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="34332-160">当可路由组件集成到 Razor Pages 应用中时，该应用的共享布局可与这些组件配合使用。</span><span class="sxs-lookup"><span data-stu-id="34332-160">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="34332-161">有关详细信息，请参阅 <xref:blazor/components/prerendering-and-integration>。</span><span class="sxs-lookup"><span data-stu-id="34332-161">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="34332-162">其他资源</span><span class="sxs-lookup"><span data-stu-id="34332-162">Additional resources</span></span>

* <xref:mvc/views/layout>
