---
title: 'ASP.NET Core :::no-loc(Blazor)::: CSS 隔离'
author: daveabrock
description: 了解如何通过 CSS 隔离将 CSS 范围限定到组件，以简化 CSS 并避免与其他组件或库发生冲突。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/components/css-isolation
ms.openlocfilehash: 628e7dc897912beaae0df792b82958517ac70ca4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056317"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a><span data-ttu-id="8e114-103">ASP.NET Core :::no-loc(Blazor)::: CSS 隔离</span><span class="sxs-lookup"><span data-stu-id="8e114-103">ASP.NET Core :::no-loc(Blazor)::: CSS isolation</span></span>

<span data-ttu-id="8e114-104">作者：[Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="8e114-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="8e114-105">CSS 隔离通过防止对全局样式的依赖来简化应用的 CSS 占用情况，帮助避免组件和库的样式冲突。</span><span class="sxs-lookup"><span data-stu-id="8e114-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="8e114-106">启用 CSS 隔离</span><span class="sxs-lookup"><span data-stu-id="8e114-106">Enable CSS isolation</span></span> 

<span data-ttu-id="8e114-107">若要定义组件特定的样式，请创建一个 `razor.css` 文件，该文件与组件的 `.razor` 文件的名称相匹配。</span><span class="sxs-lookup"><span data-stu-id="8e114-107">To define component-specific styles, create a `razor.css` file matching the name of the `.razor` file for the component.</span></span> <span data-ttu-id="8e114-108">此 `razor.css` 文件是限定范围的 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="8e114-108">This `razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="8e114-109">对于具有 `MyComponent.razor` 文件的 `MyComponent` 组件，请随组件一起创建一个名为 `MyComponent.razor.css` 的文件。</span><span class="sxs-lookup"><span data-stu-id="8e114-109">For a `MyComponent` component that has a `MyComponent.razor` file, create a file alongside the component called `MyComponent.razor.css`.</span></span> <span data-ttu-id="8e114-110">`razor.css` 文件名中的 `MyComponent` 值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="8e114-110">The `MyComponent` value in the `razor.css` filename is **not** case-sensitive.</span></span>

<span data-ttu-id="8e114-111">例如，若要将 CSS 隔离添加到默认 :::no-loc(Blazor)::: 项目模板中的 `Counter` 组件，请随 `Counter.razor` 文件一起添加一个名为 `Counter.razor.css` 的新文件，然后添加以下 CSS：</span><span class="sxs-lookup"><span data-stu-id="8e114-111">For example to add CSS isolation to the `Counter` component in the default :::no-loc(Blazor)::: project template, add a new file named `Counter.razor.css` alongside the `Counter.razor` file, then add the following CSS:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="8e114-112">`Counter.razor.css` 中定义的样式仅应用于 `Counter` 组件的呈现输出。</span><span class="sxs-lookup"><span data-stu-id="8e114-112">The styles defined in `Counter.razor.css` are only applied to the rendered output of the `Counter` component.</span></span> <span data-ttu-id="8e114-113">在应用的其他位置定义的任何 `h1` CSS 声明都不会与 `Counter` 样式冲突。</span><span class="sxs-lookup"><span data-stu-id="8e114-113">Any `h1` CSS declarations defined elsewhere in the app don't conflict with `Counter` styles.</span></span>

> [!NOTE]
> <span data-ttu-id="8e114-114">为了保证发生捆绑时的样式隔离，限定范围的 CSS 文件不支持 `@import` :::no-loc(Razor)::: 块。</span><span class="sxs-lookup"><span data-stu-id="8e114-114">In order to guarantee style isolation when bundling occurs, `@import` :::no-loc(Razor)::: blocks aren't supported with scoped CSS files.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="8e114-115">CSS 隔离捆绑</span><span class="sxs-lookup"><span data-stu-id="8e114-115">CSS isolation bundling</span></span>

<span data-ttu-id="8e114-116">CSS 隔离在生成时发生。</span><span class="sxs-lookup"><span data-stu-id="8e114-116">CSS isolation occurs at build time.</span></span> <span data-ttu-id="8e114-117">在此过程中，:::no-loc(Blazor)::: 会重写 CSS 选择器以匹配组件呈现的标记。</span><span class="sxs-lookup"><span data-stu-id="8e114-117">During this process, :::no-loc(Blazor)::: rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="8e114-118">这些重写的 CSS 样式在 `{PROJECT NAME}.styles.css` 处捆绑并生成为静态资产，其中占位符 `{PROJECT NAME}` 是引用的包或产品名称。</span><span class="sxs-lookup"><span data-stu-id="8e114-118">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="8e114-119">默认情况下，这些静态文件从应用的根路径引用。</span><span class="sxs-lookup"><span data-stu-id="8e114-119">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="8e114-120">在应用中，通过检查所生成 HTML 的 `<head>` 标记内的引用来引用捆绑的文件：</span><span class="sxs-lookup"><span data-stu-id="8e114-120">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="8e114-121">在捆绑的文件中，每个组件都与范围标识符关联。</span><span class="sxs-lookup"><span data-stu-id="8e114-121">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="8e114-122">对于每个样式的组件，HTML 属性都会追加 `b-<10-character-string>` 格式。</span><span class="sxs-lookup"><span data-stu-id="8e114-122">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="8e114-123">标识符是唯一的，每个应用各不相同。</span><span class="sxs-lookup"><span data-stu-id="8e114-123">The identifier is unique and different for each app.</span></span> <span data-ttu-id="8e114-124">在呈现的 `Counter` 组件中，:::no-loc(Blazor)::: 将范围标识符追加到 `h1` 元素：</span><span class="sxs-lookup"><span data-stu-id="8e114-124">In the rendered `Counter` component, :::no-loc(Blazor)::: appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="8e114-125">`MyProjectName.styles.css` 文件使用范围标识符将样式声明及其组件分为一组。</span><span class="sxs-lookup"><span data-stu-id="8e114-125">The `MyProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="8e114-126">下面的示例提供了前面 `<h1>` 元素的样式：</span><span class="sxs-lookup"><span data-stu-id="8e114-126">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="8e114-127">在生成时，会使用约定 `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` 创建项目捆绑包，其中占位符 `{STATIC WEB ASSETS BASE PATH}` 是静态 Web 资产的基路径。</span><span class="sxs-lookup"><span data-stu-id="8e114-127">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="8e114-128">如果利用了其他项目（如 NuGet 包或[ :::no-loc(Razor)::: 类库](xref:blazor/components/class-libraries)），则捆绑的文件将发生以下情况：</span><span class="sxs-lookup"><span data-stu-id="8e114-128">If other projects are utilized, such as NuGet packages or [:::no-loc(Razor)::: class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="8e114-129">使用 CSS 导入引用这些样式。</span><span class="sxs-lookup"><span data-stu-id="8e114-129">References the styles using CSS imports.</span></span>
* <span data-ttu-id="8e114-130">不会发布为使用这些样式的应用的静态 Web 资产。</span><span class="sxs-lookup"><span data-stu-id="8e114-130">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="8e114-131">子组件支持</span><span class="sxs-lookup"><span data-stu-id="8e114-131">Child component support</span></span>

<span data-ttu-id="8e114-132">默认情况下，CSS 隔离仅应用于与 `{COMPONENT NAME}.razor.css` 格式关联的组件，其中占位符 `{COMPONENT NAME}` 通常是组件名称。</span><span class="sxs-lookup"><span data-stu-id="8e114-132">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="8e114-133">若要对子组件应用更改，请对父组件的 `razor.css` 文件中的任何后代元素使用 `::deep` 连结符。</span><span class="sxs-lookup"><span data-stu-id="8e114-133">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `razor.css` file.</span></span> <span data-ttu-id="8e114-134">`::deep` 连结符会选择属于元素生成范围标识符后代的元素。</span><span class="sxs-lookup"><span data-stu-id="8e114-134">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="8e114-135">下面的示例演示了名为 `Parent` 的父组件和名为 `Child` 的子组件。</span><span class="sxs-lookup"><span data-stu-id="8e114-135">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="8e114-136">`Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8e114-136">`Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="8e114-137">`Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="8e114-137">`Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="8e114-138">使用 `::deep` 连结符在 `Parent.razor.css` 中更新 `h1` 声明，以表示 `h1` 样式声明必须应用于父组件及其子组件：</span><span class="sxs-lookup"><span data-stu-id="8e114-138">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="8e114-139">`h1` 样式现在将应用于 `Parent` 和 `Child` 组件，你无需为子组件创建单独的限定范围 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="8e114-139">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

> [!NOTE]
> <span data-ttu-id="8e114-140">`::deep` 连结符仅适用于后代元素。</span><span class="sxs-lookup"><span data-stu-id="8e114-140">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="8e114-141">以下 HTML 结构会按预期将 `h1` 样式应用于组件：</span><span class="sxs-lookup"><span data-stu-id="8e114-141">The following HTML structure applies the `h1` styles to components as expected:</span></span>
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> <span data-ttu-id="8e114-142">在这种情况下，ASP.NET Core 将父组件的范围标识符应用于 `div` 元素，这样浏览器便知道从父组件继承样式。</span><span class="sxs-lookup"><span data-stu-id="8e114-142">In this scenario, ASP.NET Core applies the parent component's scope identifier to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>
>
> <span data-ttu-id="8e114-143">但是，排除 `div` 元素会删除后代关系，样式将不会应用于子组件：</span><span class="sxs-lookup"><span data-stu-id="8e114-143">However, excluding the `div` element removes the descendant relationship, and the style is **not** applied to the child component:</span></span>
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a><span data-ttu-id="8e114-144">CSS 预处理器支持</span><span class="sxs-lookup"><span data-stu-id="8e114-144">CSS preprocessor support</span></span>

<span data-ttu-id="8e114-145">利用 CSS 预处理器的变量、嵌套、模块、混合和继承等功能，可有效改进 CSS 开发。</span><span class="sxs-lookup"><span data-stu-id="8e114-145">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="8e114-146">虽然 CSS 隔离并不原生支持 CSS 预处理器（如 Sass 或 Less），但只要在生成过程中 :::no-loc(Blazor)::: 重写 CSS 选择器的步骤之前进行预处理器编译，就可以无缝集成 CSS 预处理器。</span><span class="sxs-lookup"><span data-stu-id="8e114-146">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before :::no-loc(Blazor)::: rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="8e114-147">例如，使用 Visual Studio 将现有预处理器编译配置为 Visual Studio 任务运行程序资源管理器中的“生成前”任务。</span><span class="sxs-lookup"><span data-stu-id="8e114-147">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="8e114-148">许多第三方 NuGet 包（如 [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder)）都可以在生成过程开始时编译 SASS/SCSS 文件，再进行 CSS 隔离，而无需其他配置。</span><span class="sxs-lookup"><span data-stu-id="8e114-148">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="8e114-149">CSS 隔离配置</span><span class="sxs-lookup"><span data-stu-id="8e114-149">CSS isolation configuration</span></span>

<span data-ttu-id="8e114-150">CSS 隔离开箱即用，也可在某些高级场景（例如依赖于现有工具或工作流）下进行配置。</span><span class="sxs-lookup"><span data-stu-id="8e114-150">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="8e114-151">自定义范围标识符格式</span><span class="sxs-lookup"><span data-stu-id="8e114-151">Customize scope identifier format</span></span>

<span data-ttu-id="8e114-152">默认情况下，范围标识符使用 `b-<10-character-string>` 格式。</span><span class="sxs-lookup"><span data-stu-id="8e114-152">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="8e114-153">若要自定义范围标识符格式，请将项目文件更新为所需模式：</span><span class="sxs-lookup"><span data-stu-id="8e114-153">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="8e114-154">在上面的示例中，为 `MyComponent.:::no-loc(Razor):::.css` 生成的 CSS 将其范围标识符从 `b-<10-character-string>` 更改为了 `my-custom-scope-identifier`。</span><span class="sxs-lookup"><span data-stu-id="8e114-154">In the preceding example, the CSS generated for `MyComponent.:::no-loc(Razor):::.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="8e114-155">更改静态 Web 资产的基路径</span><span class="sxs-lookup"><span data-stu-id="8e114-155">Change base path for static web assets</span></span>

<span data-ttu-id="8e114-156">`scoped.styles.css` 文件在应用的根目录生成。</span><span class="sxs-lookup"><span data-stu-id="8e114-156">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="8e114-157">在项目文件中，请使用 `StaticWebAssetBasePath` 属性来更改默认路径。</span><span class="sxs-lookup"><span data-stu-id="8e114-157">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="8e114-158">下面的示例将 `scoped.styles.css` 文件以及应用的其余资产放在 `_content` 路径：</span><span class="sxs-lookup"><span data-stu-id="8e114-158">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="8e114-159">禁用自动捆绑</span><span class="sxs-lookup"><span data-stu-id="8e114-159">Disable automatic bundling</span></span>

<span data-ttu-id="8e114-160">若要禁用 :::no-loc(Blazor)::: 在运行时发布和加载限定范围的文件，请使用 `DisableScopedCssBundling` 属性。</span><span class="sxs-lookup"><span data-stu-id="8e114-160">To opt out of how :::no-loc(Blazor)::: publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="8e114-161">使用此属性时，意味着将由其他工具或进程从 `obj` 目录中捕获隔离的 CSS 文件，并在运行时发布和加载这些文件：</span><span class="sxs-lookup"><span data-stu-id="8e114-161">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```
