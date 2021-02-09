---
title: ASP.NET Core Blazor CSS 隔离
author: daveabrock
description: 了解如何通过 CSS 隔离将 CSS 范围限定到组件，以简化 CSS 并避免与其他组件或库发生冲突。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 0748f606314963788e6733ca2ae2ca2123d839b3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529977"
---
# <a name="aspnet-core-blazor-css-isolation"></a>ASP.NET Core Blazor CSS 隔离

作者：[Dave Brock](https://twitter.com/daveabrock)

CSS 隔离通过防止对全局样式的依赖来简化应用的 CSS 占用情况，帮助避免组件和库的样式冲突。

## <a name="enable-css-isolation"></a>启用 CSS 隔离 

若要定义组件特定的样式，请在相同文件夹中创建一个 `.razor.css` 文件，该文件与组件的 `.razor` 文件的名称相匹配。 `.razor.css` 文件是限定范围的 CSS 文件。 

对于 `Example.razor` 文件中的 `Example` 组件，请随组件一起创建一个名为 `Example.razor.css` 的文件。 `Example.razor.css` 文件必须驻留在与 `Example` 组件 (`Example.razor`) 相同的文件夹中。 文件的“`Example`”基名称不区分大小写。

`Pages/Example.razor`:

```razor
@page "/example"

<h1>Scoped CSS Example</h1>
```

`Pages/Example.razor.css`:

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

`Example.razor.css` 中定义的样式仅应用于 `Example` 组件的呈现输出。 CSS 隔离适用于匹配的 Razor 文件中的 HTML 元素。 在应用的其他位置定义的任何 `h1` CSS 声明都不会与 `Example` 组件的样式冲突。

> [!NOTE]
> 为了保证发生捆绑时的样式隔离，不支持在 Razor 代码块中导入 CSS。

## <a name="css-isolation-bundling"></a>CSS 隔离捆绑

CSS 隔离在生成时发生。 在此过程中，Blazor 会重写 CSS 选择器以匹配组件呈现的标记。 这些重写的 CSS 样式在 `{PROJECT NAME}.styles.css` 处捆绑并生成为静态资产，其中占位符 `{PROJECT NAME}` 是引用的包或产品名称。

默认情况下，这些静态文件从应用的根路径引用。 在应用中，通过检查所生成 HTML 的 `<head>` 标记内的引用来引用捆绑的文件：

```html
<link href="ProjectName.styles.css" rel="stylesheet">
```

在捆绑的文件中，每个组件都与范围标识符关联。 对于每个样式的组件，HTML 属性都会追加 `b-<10-character-string>` 格式。 标识符是唯一的，每个应用各不相同。 在呈现的 `Counter` 组件中，Blazor 将范围标识符追加到 `h1` 元素：

```html
<h1 b-3xxtam6d07>
```

`ProjectName.styles.css` 文件使用范围标识符将样式声明及其组件分为一组。 下面的示例提供了前面 `<h1>` 元素的样式：

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

在生成时，会使用约定 `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css` 创建项目捆绑包，其中占位符 `{STATIC WEB ASSETS BASE PATH}` 是静态 Web 资产的基路径。

如果利用了其他项目（如 NuGet 包或[ Razor 类库](xref:blazor/components/class-libraries)），则捆绑的文件将发生以下情况：

* 使用 CSS 导入引用这些样式。
* 不会发布为使用这些样式的应用的静态 Web 资产。

## <a name="child-component-support"></a>子组件支持

默认情况下，CSS 隔离仅应用于与 `{COMPONENT NAME}.razor.css` 格式关联的组件，其中占位符 `{COMPONENT NAME}` 通常是组件名称。 若要对子组件应用更改，请对父组件的 `.razor.css` 文件中的任何后代元素使用 `::deep` 连结符。 `::deep` 连结符会选择属于元素生成范围标识符后代的元素。 

下面的示例演示了名为 `Parent` 的父组件和名为 `Child` 的子组件。

`Pages/Parent.razor`:

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

`Shared/Child.razor`:

```razor
<h1>Child Component</h1>
```

使用 `::deep` 连结符在 `Parent.razor.css` 中更新 `h1` 声明，以表示 `h1` 样式声明必须应用于父组件及其子组件。

`Pages/Parent.razor.css`:

```css
::deep h1 { 
    color: red;
}
```

`h1` 样式现在将应用于 `Parent` 和 `Child` 组件，你无需为子组件创建单独的限定范围 CSS 文件。

`::deep` 连结符仅适用于后代元素。 以下标记会按预期将 `h1` 样式应用于组件。 父组件的范围标识符应用于 `div` 元素，这样浏览器便知道从父组件继承样式。

`Pages/Parent.razor`:

```razor
<div>
    <h1>Parent</h1>

    <Child />
</div>
```

但是，排除 `div` 元素将删除后代关系。 在下面的示例中，样式不应用于子组件。

`Pages/Parent.razor`:

```razor
<h1>Parent</h1>

<Child />
```

## <a name="css-preprocessor-support"></a>CSS 预处理器支持

利用 CSS 预处理器的变量、嵌套、模块、混合和继承等功能，可有效改进 CSS 开发。 虽然 CSS 隔离并不原生支持 CSS 预处理器（如 Sass 或 Less），但只要在生成过程中 Blazor 重写 CSS 选择器的步骤之前进行预处理器编译，就可以无缝集成 CSS 预处理器。 例如，使用 Visual Studio 将现有预处理器编译配置为 Visual Studio 任务运行程序资源管理器中的“生成前”任务。

许多第三方 NuGet 包（如 [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder)）都可以在生成过程开始时编译 SASS/SCSS 文件，再进行 CSS 隔离，而无需其他配置。

## <a name="css-isolation-configuration"></a>CSS 隔离配置

CSS 隔离开箱即用，也可在某些高级场景（例如依赖于现有工具或工作流）下进行配置。

### <a name="customize-scope-identifier-format"></a>自定义范围标识符格式

默认情况下，范围标识符使用 `b-<10-character-string>` 格式。 若要自定义范围标识符格式，请将项目文件更新为所需模式：

```xml
<ItemGroup>
  <None Update="Pages/Example.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

在上面的示例中，为 `Example.Razor.css` 生成的 CSS 将其范围标识符从 `b-<10-character-string>` 更改为了 `my-custom-scope-identifier`。

使用范围标识符来实现与限定范围的 CSS 文件的继承。 在下面的项目文件示例中，`BaseComponent.razor.css` 文件包含跨组件的通用样式。 `DerivedComponent.razor.css` 文件继承了这些样式。

```xml
<ItemGroup>
  <None Update="Pages/BaseComponent.razor.css" CssScope="my-custom-scope-identifier" />
  <None Update="Pages/DerivedComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

使用通配符 (`*`) 运算符跨多个文件共享范围标识符：

```xml
<ItemGroup>
  <None Update="Pages/*.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

### <a name="change-base-path-for-static-web-assets"></a>更改静态 Web 资产的基路径

`scoped.styles.css` 文件在应用的根目录生成。 在项目文件中，请使用 `StaticWebAssetBasePath` 属性来更改默认路径。 下面的示例将 `scoped.styles.css` 文件以及应用的其余资产放在 `_content` 路径：

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a>禁用自动捆绑

若要禁用 Blazor 在运行时发布和加载限定范围的文件，请使用 `DisableScopedCssBundling` 属性。 使用此属性时，意味着将由其他工具或进程从 `obj` 目录中捕获隔离的 CSS 文件，并在运行时发布和加载这些文件：

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="razor-class-library-rcl-support"></a>Razor 类库 (RCL) 支持

[Razor 类库 (RCL)](xref:razor-pages/ui-class) 提供隔离的样式，`<link>` 标记的 `href` 属性指向 `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`，其中占位符为：

* `{STATIC WEB ASSET BASE PATH}`：静态 Web 资产基路径。
* `{ASSEMBLY NAME}`：类库的程序集名称。

如下示例中：

* 静态 Web 资产基路径为 `_content/ClassLib`。
* 类库的程序集名称为 `ClassLib`。

`wwwroot/index.html` (Blazor WebAssembly) 或 `Pages_Host.cshtml` (Blazor Server)：

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

有关 RCL 和组件库的详细信息，请参阅：

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>.
